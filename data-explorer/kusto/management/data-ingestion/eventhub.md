---
title: Von Event Hub erfassen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Ingest from Event Hub in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ddb218e707152f529e5b547ffe06c4d3c7614811
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521504"
---
# <a name="ingest-from-event-hub"></a>Aufnahme von Event Hub

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst. Azure Data Explorer bietet kontinuierliche Erfassung von vom Kunden verwalteten Event Hubs. 

## <a name="data-format"></a>Datenformat

* Daten werden aus dem Event Hub in Form von [EventData-Objekten](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) gelesen.
* Die Ereignisnutzlast kann einen oder mehrere zu verführende Datensätze in einem der [von Azure Data Explorer unterstützten Formate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)enthalten.
* Daten können mit `GZip` einem Komprimierungsalgorithmus komprimiert werden. Muss als `Compression` [Aufnahmeeigenschaft](#ingestion-properties)angegeben werden.

> [!Note]
> * Die Datenkomprimierung wird für komprimierte Formate (Avro, Parquet, ORC) nicht unterstützt.
> * Benutzerdefinierte Codierungs- und einbettete [Systemeigenschaften](#event-system-properties-mapping) werden für komprimierte Daten nicht unterstützt.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Die Aufnahmeeigenschaften weisen den Aufnahmeprozess an. Wo die Daten weitergeleitet werden und wie sie verarbeitet werden. Sie können [die Aufnahmeeigenschaften](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) der Ereignisse mithilfe der [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (Groß-/Kleinschreibung) der vorhandenen Zieltabelle. Überschreibt `Table` den Satz `Data Connection` auf dem Blatt. |
| Format | Datenformat. Überschreibt `Data format` den Satz `Data Connection` auf dem Blatt. |
| IngestionMappingReference | Name der vorhandenen [Erfassungszuordnung,](../create-ingestion-mapping-command.md) die verwendet werden soll. Überschreibt `Column mapping` den Satz `Data Connection` auf dem Blatt.|
| Komprimierung | Datenkomprimierung `None` (Standard) `GZip` oder Komprimierung.|
| Codieren |  Datencodierung, die Standardeinstellung ist UTF8. Es kann sich um eine von [.NET unterstützte Codierung sein.](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks) |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Ereignisrouting

Beim Einrichten einer Event Hub-Verbindung mit dem Azure Data Explorer-Cluster geben Sie Die Eigenschaften der Zieltabelle (Tabellenname, Datenformat, Komprimierung und Zuordnung) an. Dies ist das Standardrouting für Ihre `static routing`Daten, auch als bezeichnet.
Sie können auch Zieltabelleneigenschaften für jedes Ereignis mithilfe von Ereigniseigenschaften angeben. Die Verbindung leitet die Daten dynamisch weiter, wie in [den EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)angegeben, und überschreibt die statischen Eigenschaften für dieses Ereignis.

Legen Sie im folgenden Beispiel Ereignishubdetails fest, `WeatherMetrics`und senden Sie Wettermetrikdaten an die Tabelle .
Die Daten `json` sind im Format. `mapping1`ist in der Tabelle `WeatherMetrics`vordefiniert:

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

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>Zuordnung von Ereignissystemeigenschaften

Systemeigenschaften sind eine Auflistung, die zum Speichern von Eigenschaften verwendet wird, die vom Event Hubs-Dienst zum Zeitpunkt der Warteschlange des Ereignisses festgelegt werden. Die Azure Data Explorer Event Hub-Verbindung bettet die ausgewählten Eigenschaften in die Datenlandung in der Tabelle ein.

> [!Note]
> * Systemeigenschaften werden für Ereignisse mit einem Datensatz unterstützt.
> * Systemeigenschaften werden für komprimierte Daten nicht unterstützt.
> * Für `csv` die Zuordnung werden Eigenschaften am Anfang des Datensatzes in der Reihenfolge hinzugefügt, die in der folgenden Tabelle aufgeführt ist. Für `json` die Zuordnung werden Eigenschaften entsprechend den Eigenschaftsnamen in der folgenden Tabelle hinzugefügt.

### <a name="event-hub-expose-the-following-system-properties"></a>Event Hub macht die folgenden Systemeigenschaften verfügbar

|Eigenschaft |Datentyp |Beschreibung|
|---|---|---|
| x-opt-enqueued-time |datetime | UTC-Zeit, zu der das Ereignis in die Warteschlange eingereiht wurde. |
| x-opt-sequence-number |long | Die logische Sequenznummer des Ereignisses innerhalb des Partitionsstreams des Event Hubs.
| x-opt-offset |Zeichenfolge | Der Offset des Ereignisses relativ zum Event Hub-Partitionsstream. Der Offsetbezeichner ist innerhalb einer Partition des Event Hub-Streams eindeutig. |
| x-opt-Publisher |Zeichenfolge | Der Herausgebername, wenn die Nachricht an einen Herausgeberendpunkt gesendet wurde. |
| x-opt-partition-key |Zeichenfolge |Der Partitionsschlüssel der entsprechenden Partition, die das Ereignis gespeichert hat. |

Wenn Sie **Ereignissystemeigenschaften** im Abschnitt **Datenquelle** der Tabelle ausgewählt haben, müssen Sie die Eigenschaften in das Tabellenschema und die Zuordnung einbeziehen.

**Tabellenschemabeispiel**

Wenn Ihre Daten drei`Timespan` `Metric`Spalten `Value`( , , und `x-opt-enqueued-time` `x-opt-offset`) enthalten und die Eigenschaften, die Sie einschließen, sind und , erstellen oder ändern Sie das Tabellenschema mit diesem Befehl:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV-Zuordnungsbeispiel**

Führen Sie die folgenden Befehle aus, um Daten am Anfang des Datensatzes hinzuzufügen. Anmerkung Ordinalwerte: Eigenschaften werden am Anfang des Datensatzes in der in der obigen Tabelle aufgeführten Reihenfolge hinzugefügt. Dies ist wichtig für die CSV-Zuordnung, bei der sich die Spaltenordinals basierend auf den zugeordneten Systemeigenschaften ändern.

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
 
**JSON-Zuordnungsbeispiel**

Daten werden mithilfe der Systemeigenschaftennamen hinzugefügt, wie sie in der Liste **Ereignissystemeigenschaften** für die **Datenverbindung** angezeigt werden. Führen Sie diese Befehle aus.

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
> Um eine optimale Leistung zu erzielen, erstellen Sie alle Ressourcen in derselben Region wie der Azure Data Explorer-Cluster.

### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Wenn Sie noch keinen haben, [erstellen Sie einen Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create). Eine Vorlage finden Sie im Anleitungshandbuch zum [Erstellen eines Eventhubs.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub)

> [!Note]
> * Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf.
> * Die Verbrauchergruppe *muss* pro Verbraucher uniqe sein. Erstellen Sie eine Consumergruppe, die der Azure Data Explorer-Verbindung gewidmet ist.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Datenerfassungsverbindung mit Azure Data Explorer

* Über Azure Portal: [Stellen Sie eine Verbindung mit dem Event Hub her.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)
* Verwenden der Azure Data Explorer-Verwaltung .NET SDK: [Hinzufügen einer Event Hub-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Verwenden der Azure Data Explorer-Verwaltung Python SDK: [Hinzufügen einer Event Hub-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* Mit ARM-Vorlage: [Azure Resource Manager-Vorlage zum Hinzufügen einer Event Hub-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Wenn **Meine Daten Routinginformationen enthalten,** *müssen* Sie die erforderlichen [Routinginformationen](#events-routing) als Teil der Ereigniseigenschaften angeben.

> [!Note]
> Sobald die Verbindung festgelegt ist, werden Daten erfasst, die von Ereignissen ausgehen, die nach der Erstellungszeit in die Warteschlange eingereiht wurden.

#### <a name="generating-data"></a>Generieren von Daten

* Sehen Sie sich die [Beispiel-App an,](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) die Daten generiert und an einen Event Hub sendet.

Ein Ereignis kann einen oder mehrere Datensätze bis zu seiner Größenbeschränkung enthalten. Im folgenden Beispiel senden wir zwei Ereignisse, denen jeweils 5 Datensätze angehängt sind:

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