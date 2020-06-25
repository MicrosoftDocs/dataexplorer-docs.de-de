---
title: Erfassung von Event Hub-Azure-Daten-Explorer
description: Dieser Artikel beschreibt die Erfassung von Event Hub in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: dbb74d8b718b5a03fa0e9e7f4679f48c837d0842
ms.sourcegitcommit: c3bbb9a6bfd7c5506f05afb4968fdc2043a9fbbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85332541"
---
# <a name="ingest-from-event-hub"></a>Erfassung aus Event Hub

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) ist eine Big Data streamingplattform und ein Dienst zur Erfassung von Ereignissen. Azure Daten-Explorer bietet fortlaufende Erfassung von Kunden verwalteten Event Hubs.

## <a name="data-format"></a>Datenformat

* Daten werden aus dem Event Hub in Form von [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) -Objekten gelesen.
* Die Ereignis Nutzlast kann einen oder mehrere Datensätze enthalten, die in einem der [von Azure Daten-Explorer unterstützten Formate](../../../ingestion-supported-formats.md)erfasst werden.
* Daten können mithilfe des `GZip` Komprimierungs Algorithmus komprimiert werden. Muss als Erfassungs `Compression` [Eigenschaft](#ingestion-properties)angegeben werden.

> [!Note]
> * Die Datenkomprimierung wird für komprimierte Formate (Avro, Parkett, Orc) nicht unterstützt.
> * Benutzerdefinierte Codierungen und eingebettete [Systemeigenschaften](#event-system-properties-mapping) werden für komprimierte Daten nicht unterstützt.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Erfassungs Eigenschaften weisen den Erfassungs Vorgang an, wo die Daten weitergeleitet werden und wie Sie verarbeitet werden. Mithilfe von [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)können Sie Erfassungs [Eigenschaften](../../../ingestion-properties.md) der Ereignis Erfassung angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (Groß-/Kleinschreibung beachten) der vorhandenen Ziel Tabelle. Überschreibt das `Table`, das auf dem Blatt `Data Connection` festgelegt ist. |
| Format | Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| Ingestionmappingreferenzierung | Der Name der vorhandenen Erfassungs [Zuordnung](../create-ingestion-mapping-command.md) , die verwendet werden soll. Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| Komprimierung | Datenkomprimierung, `None` (Standard) oder `GZip` Komprimierung.|
| Codierung | Daten Codierung, der Standardwert ist UTF8. Kann eine beliebige von [.NET unterstützte Codierungen](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)sein. |
| Tags (Vorschau) | Eine Liste von [Tags](../extents-overview.md#extent-tagging) , die den erfassten Daten zugeordnet werden sollen, die als JSON-Array Zeichenfolge formatiert sind. Die Verwendung von Tags wirkt sich auf die [Leistung](../extents-overview.md#performance-notes-1) aus. |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Ereignis Routing

Wenn Sie eine Event Hub-Verbindung mit Azure Daten-Explorer-Cluster einrichten, geben Sie die Ziel Tabellen Eigenschaften an (Tabellenname, Datenformat, Komprimierung und Zuordnung). Das Standard Routing für Ihre Daten wird auch als bezeichnet `static routing` .
Sie können auch die Ziel Tabellen Eigenschaften für jedes Ereignis mithilfe von Ereignis Eigenschaften angeben. Die Verbindung leitet die Daten dynamisch entsprechend der Angabe in [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)weiter und überschreibt die statischen Eigenschaften für dieses Ereignis.

Legen Sie im folgenden Beispiel Event Hub Details fest, und senden Sie Wetter Metrikdaten an Table `WeatherMetrics` .
Die Daten sind im `json` Format. `mapping1`ist für die Tabelle vordefiniert `WeatherMetrics` .

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>Zuordnung von Ereignissystemeigenschaften

System Eigenschaften speichern Eigenschaften, die vom Event Hubs-Dienst festgelegt werden, zu dem Zeitpunkt, zu dem das Ereignis in die Warteschlange eingereiht wird. Die Azure Daten-Explorer Event Hub-Verbindung bettet die ausgewählten Eigenschaften in die Daten in der Tabelle ein.

> [!Note]
> * System Eigenschaften werden für Single-Record-Ereignisse unterstützt.
> * System Eigenschaften werden für komprimierte Daten nicht unterstützt.
> * Für die `csv` Zuordnung werden Eigenschaften am Anfang des Datensatzes in der in der folgenden Tabelle aufgeführten Reihenfolge hinzugefügt. Für `json` die Zuordnung werden Eigenschaften gemäß den Eigenschaftsnamen in der folgenden Tabelle hinzugefügt.

### <a name="event-hub-exposes-the-following-system-properties"></a>Event Hub macht die folgenden Systemeigenschaften verfügbar.

|Eigenschaft |Datentyp |BESCHREIBUNG|
|---|---|---|
| x-opt-enqueued-time |datetime | UTC-Zeit, wann das Ereignis in die Warteschlange eingereiht wurde |
| x-opt-sequence-number |long | Die logische Sequenznummer des Ereignisses innerhalb des Partitions Datenstroms des Event Hubs.
| x-opt-offset |Zeichenfolge | Der Offset des Ereignisses aus dem Event Hub-Partitions Datenstrom. Der Offset Bezeichner ist innerhalb einer Partition des Event Hub-Streams eindeutig. |
| x-opt-Publisher |Zeichenfolge | Der Name des Verlegers, wenn die Nachricht an einen Verleger Endpunkt gesendet wurde. |
| x-opt-partition-key |Zeichenfolge |Der Partitions Schlüssel der entsprechenden Partition, die das Ereignis gespeichert hat. |

Wenn Sie im Abschnitt **Datenquelle** der Tabelle die Option **Ereignis Systemeigenschaften** ausgewählt haben, müssen Sie die Eigenschaften in das Tabellen Schema und die Zuordnung einschließen.

**Beispiel für Tabellen Schema**

Erstellen oder ändern Sie das Tabellen Schema mit dem Befehl Table Schema, wenn die Daten Folgendes enthalten:
* die Spalten `Timespan` , `Metric` und.`Value`  
* die Eigenschaften `x-opt-enqueued-time` und`x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Beispiel für CSV-Zuordnung**

Führen Sie die folgenden Befehle aus, um am Anfang des Datensatzes Daten hinzuzufügen.
Eigenschaften werden am Anfang des Datensatzes in der in der obigen Tabelle aufgeführten Reihenfolge hinzugefügt.
Die Ordinalwerte sind wichtig für die CSV-Zuordnung, bei der die Spalten Ordnungszahlen basierend auf den zugeordneten Systemeigenschaften geändert werden.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Beispiel für JSON-Zuordnung**

Fügen Sie Daten hinzu, indem Sie die Namen der Systemeigenschaften verwenden, die auf der Liste der *Ereignis Systemeigenschaften* des *Daten Verbindungs* Blatts angezeigt werden. Führen Sie folgenden Befehl aus:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

## <a name="create-event-hub-connection"></a>Event Hub Verbindung erstellen

> [!Note]
> Erstellen Sie für eine optimale Leistung alle Ressourcen in der gleichen Region wie der Azure Daten-Explorer-Cluster.

### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Wenn Sie noch keine haben, [Erstellen Sie eine Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create). Eine Vorlage finden Sie im Leitfaden How-to [Create an Event Hub](../../../ingest-data-event-hub.md#create-an-event-hub) .

> [!Note]
> * Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf.
> * Consumergruppe *muss* pro Consumer eindeutig sein. Erstellen Sie eine consumergruppe, die für Azure Daten-Explorer Verbindung reserviert ist.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Verbindung mit der Datenerfassung mit Azure Daten-Explorer

* Über Azure-Portal: stellen [Sie eine Verbindung mit dem Event Hub](../../../ingest-data-event-hub.md#connect-to-the-event-hub)her.
* Verwenden des Azure Daten-Explorer Management .NET SDK: [Hinzufügen einer Event Hub-Datenverbindung](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)
* Verwenden des Azure Daten-Explorer Management python SDK: [Hinzufügen einer Event Hub-Datenverbindung](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)
* Mit Arm-Vorlage: Verwenden Sie [Azure Resource Manager Vorlage zum Hinzufügen einer Event Hub-Datenverbindung](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection) .

> [!Note]
> Wenn **meine Daten Routing Informationen enthalten** , *müssen* Sie die erforderlichen [Routing](#events-routing) Informationen als Teil der Ereignis Eigenschaften bereitstellen.

> [!Note]
> Nachdem die Verbindung festgelegt wurde, erfasst Sie Daten, beginnend bei Ereignissen, die nach dem Erstellungs Zeitpunkt in die Warteschlange eingereiht wurden.

#### <a name="generating-data"></a>Generieren von Daten

* Weitere Informationen finden Sie in der [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , die Daten generiert und an eine Event Hub sendet.

Ein Ereignis kann einen oder mehrere Datensätze bis zum Größenlimit enthalten. Im folgenden Beispiel werden zwei Ereignisse gesendet, von denen jeweils fünf Datensätze angehängt werden:

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```
