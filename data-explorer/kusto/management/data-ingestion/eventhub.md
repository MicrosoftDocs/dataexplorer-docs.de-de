---
title: Erfassung von Event Hub-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Erfassung von Event Hub in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: a9308fa762bf7bbda0f57a1c252cf2121253565a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373466"
---
# <a name="ingest-from-event-hub"></a>Erfassung aus Event Hub

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) ist eine Big Data streamingplattform und ein Dienst zur Erfassung von Ereignissen. Azure Daten-Explorer bietet fortlaufende Erfassung von Kunden verwalteten Event Hubs. 

## <a name="data-format"></a>Datenformat

* Daten werden aus dem Event Hub in Form von [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) -Objekten gelesen.
* Die Ereignis Nutzlast kann einen oder mehrere Datensätze enthalten, die erfasst werden sollen, und zwar in einem der Formate, die [von Azure Daten-Explorer unterstützt](../../../ingestion-supported-formats.md)werden.
* Daten können mithilfe des `GZip` Komprimierungs Algorithmus komprimiert werden. Muss als Erfassungs `Compression` [Eigenschaft](#ingestion-properties)angegeben werden.

> [!Note]
> * Die Datenkomprimierung wird für komprimierte Formate (Avro, Parkett, Orc) nicht unterstützt.
> * Benutzerdefinierte Codierungen und eingebettete [Systemeigenschaften](#event-system-properties-mapping) werden für komprimierte Daten nicht unterstützt.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Erfassungs Eigenschaften weist den Erfassungsprozess an. Wo die Daten weitergeleitet werden und wie Sie verarbeitet werden. Mithilfe von [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)können Sie Erfassungs [Eigenschaften](../../../ingestion-properties.md) der Ereignis Erfassung angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (Groß-/Kleinschreibung beachten) der vorhandenen Ziel Tabelle. Überschreibt das `Table`, das auf dem Blatt `Data Connection` festgelegt ist. |
| Format | Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| Ingestionmappingreferenzierung | Der Name der vorhandenen Erfassungs [Zuordnung](../create-ingestion-mapping-command.md) , die verwendet werden soll. Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| Komprimierung | Datenkomprimierung, `None` (Standard) oder `GZip` Komprimierung.|
| Codieren |  Daten Codierung, der Standardwert ist UTF8. Kann eine beliebige von [.NET unterstützte Codierungen](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)sein. |
| Tags (Vorschau) | Eine Liste von [Tags](../extents-overview.md#extent-tagging) , die den erfassten Daten zugeordnet werden sollen, die als JSON-Array Zeichenfolge formatiert sind. Beachten Sie die Auswirkungen der Verwendung von Tags auf die [Leistung](../extents-overview.md#performance-notes-1) . |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Ereignis Routing

Beim Einrichten einer Event Hub-Verbindung mit dem Azure Daten-Explorer-Cluster geben Sie die Ziel Tabellen Eigenschaften an (Tabellenname, Datenformat, Komprimierung und Zuordnung). Dies ist das Standard Routing für Ihre Daten, das auch als bezeichnet wird `static routing` .
Sie können auch die Ziel Tabellen Eigenschaften für jedes Ereignis mithilfe von Ereignis Eigenschaften angeben. Die Verbindung leitet die Daten dynamisch entsprechend der Angabe in [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)weiter und überschreibt die statischen Eigenschaften für dieses Ereignis.

Legen Sie im folgenden Beispiel Event Hub Details fest, und senden Sie Wetter Metrikdaten an Table `WeatherMetrics` .
Die Daten sind im `json` Format. `mapping1`ist für die Tabelle vordefiniert `WeatherMetrics` :

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

System Eigenschaften sind eine Sammlung zum Speichern von Eigenschaften, die vom Event Hubs-Dienst festgelegt werden, wenn das Ereignis in die Warteschlange eingereiht wird. Die Azure Daten-Explorer Event Hub-Verbindung bettet die ausgewählten Eigenschaften in die Daten in der Tabelle ein.

> [!Note]
> * System Eigenschaften werden für Single-Record-Ereignisse unterstützt.
> * System Eigenschaften werden für komprimierte Daten nicht unterstützt.
> * Für die `csv` Zuordnung werden Eigenschaften am Anfang des Datensatzes in der in der folgenden Tabelle aufgeführten Reihenfolge hinzugefügt. Für `json` die Zuordnung werden Eigenschaften gemäß den Eigenschaftsnamen in der folgenden Tabelle hinzugefügt.

### <a name="event-hub-expose-the-following-system-properties"></a>Event Hub macht die folgenden Systemeigenschaften verfügbar.

|Eigenschaft |Datentyp |BESCHREIBUNG|
|---|---|---|
| x-opt-enqueued-time |datetime | UTC-Zeit, zu der das Ereignis in die Warteschlange eingereiht wurde |
| x-opt-sequence-number |long | Die logische Sequenznummer des Ereignisses innerhalb des Partitions Datenstroms des Event Hubs.
| x-opt-offset |string | Der Offset des Ereignisses relativ zum Event Hub-Partitions Datenstrom. Der Offset Bezeichner ist innerhalb einer Partition des Event Hub-Streams eindeutig. |
| x-opt-Publisher |string | Der Name des Verlegers, wenn die Nachricht an einen Verleger Endpunkt gesendet wurde. |
| x-opt-partition-key |string |Der Partitions Schlüssel der entsprechenden Partition, die das Ereignis gespeichert hat. |

Wenn Sie im Abschnitt **Datenquelle** der Tabelle die Option **Ereignis Systemeigenschaften** ausgewählt haben, müssen Sie die Eigenschaften in das Tabellen Schema und die Zuordnung einschließen.

**Beispiel für Tabellen Schema**

Wenn die Daten drei Spalten ( `Timespan` , `Metric` und) enthalten `Value` und die Eigenschaften, die Sie einschließen `x-opt-enqueued-time` , und sind `x-opt-offset` , erstellen oder ändern Sie das Tabellen Schema mit dem folgenden Befehl:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Beispiel für CSV-Zuordnung**

Führen Sie die folgenden Befehle aus, um am Anfang des Datensatzes Daten hinzuzufügen. Hinweis Ordinalwerte: die Eigenschaften werden am Anfang des Datensatzes in der in der obigen Tabelle aufgeführten Reihenfolge hinzugefügt. Dies ist wichtig für die CSV-Zuordnung, bei der sich die Spalten Ordnungszahlen basierend auf den zugeordneten Systemeigenschaften ändern.

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

Daten werden mithilfe der System Eigenschaftsnamen hinzugefügt, die auf der Liste der **Ereignis Systemeigenschaften** des **Daten Verbindungs** Blatts angezeigt werden. Führen Sie diese Befehle aus:

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

## <a name="create-event-hub-connection"></a>Herstellen einer Event Hub-Verbindung

> [!Note]
> Erstellen Sie für eine optimale Leistung alle Ressourcen in der gleichen Region wie der Azure Daten-Explorer-Cluster.

### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Wenn Sie noch keine haben, [Erstellen Sie eine Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create). Eine Vorlage finden Sie im Leitfaden How-to [Create an Event Hub](../../../ingest-data-event-hub.md#create-an-event-hub) .

> [!Note]
> * Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf.
> * Consumergruppe *muss* uniqe pro Consumer sein. Erstellen Sie eine consumergruppe, die für Azure Daten-Explorer Verbindung reserviert ist.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Verbindung mit der Datenerfassung mit Azure Daten-Explorer

* Über das Azure-Portal: stellen [Sie eine Verbindung zum Event Hub](../../../ingest-data-event-hub.md#connect-to-the-event-hub)her.
* Verwenden des Azure Daten-Explorer Management .NET SDK: [Hinzufügen einer Event Hub-Datenverbindung](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)
* Verwenden des Azure Daten-Explorer Management python SDK: [Hinzufügen einer Event Hub-Datenverbindung](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)
* Mit Arm-Vorlage: [Azure Resource Manager Vorlage zum Hinzufügen einer Event Hub-Datenverbindung](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Wenn **meine Daten Routing Informationen enthalten** , *müssen* Sie die erforderlichen [Routing](#events-routing) Informationen als Teil der Ereignis Eigenschaften bereitstellen.

> [!Note]
> Nachdem die Verbindung festgelegt wurde, erfassen Sie Daten, beginnend bei Ereignissen, die nach dem Erstellungs Zeitpunkt in die Warteschlange eingereiht wurden.

#### <a name="generating-data"></a>Erstellen von Daten

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
