---
title: 'Erfassen von Daten aus Event Hub: Azure Data Explorer'
description: In diesem Artikel wird die Erfassung von Daten aus Event Hub in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 5dadde42bcdade8d7839c149cf7ca335b8a49bc8
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202411"
---
# <a name="connect-to-event-hub"></a>Herstellen einer Event Hub-Verbindung


Bei [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) handelt es sich um eine Big Data-Streamingplattform und einen Ereigniserfassungsdienst. Azure Data Explorer bietet eine kontinuierliche Erfassung von Daten aus kundenseitig verwalteten Event Hubs.

Die Event Hub-Erfassungspipeline überträgt Ereignisse in mehreren Schritten an Azure Data Explorer. Zunächst erstellen Sie im Azure-Portal einen Event Hub. Anschließend erstellen Sie eine Zieltabelle in Azure Data Explorer, in der die [Daten in einem bestimmten Format](#data-format) anhand der [Erfassungseigenschaften](#set-ingestion-properties) erfasst werden. Die Event Hub-Verbindung muss über Informationen zum [Ereignisrouting](#set-events-routing) verfügen. Daten werden mit ausgewählten Eigenschaften gemäß der [Eigenschaftenzuordnung des Ereignissystems](#set-event-system-properties-mapping) eingebettet. Dieser Prozess kann über das [Azure-Portal](ingest-data-event-hub.md), programmgesteuert mit [C#](data-connection-event-hub-csharp.md) oder [Python](data-connection-event-hub-python.md) oder mit der [Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md) verwaltet werden.

## <a name="data-format"></a>Datenformat

* Daten werden in Form von [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)-Objekten aus dem Event Hub gelesen.
* Die Ereignisnutzdaten können in einem der [von Azure Data Explorer unterstützten Formate](ingestion-supported-formats.md) erfasst werden.
* Daten können mit dem `GZip`-Komprimierungsalgorithmus komprimiert werden. Dies muss mit der [Erfassungseigenschaft](#set-ingestion-properties) `Compression` angegeben werden.

    > [!Note]
    > * Für komprimierte Formate (Avro, Parquet, ORC) wird die Datenkomprimierung nicht unterstützt.
    > * Eine benutzerdefinierte Codierung und eingebettete [Systemeigenschaften](#set-event-system-properties-mapping) werden für komprimierte Daten nicht unterstützt.
    
## <a name="set-ingestion-properties"></a>Festlegen von Erfassungseigenschaften

Erfassungseigenschaften weisen den Erfassungsprozess an, wohin die Daten weitergeleitet und wie sie verarbeitet werden sollen. Sie können [Erfassungseigenschaften](ingestion-properties.md) der Ereigniserfassung mithilfe von [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (unter Berücksichtigung der Groß-/Kleinschreibung) der vorhandenen Zieltabelle. Überschreibt das `Table`, das auf dem Blatt `Data Connection` festgelegt ist. |
| Format | Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| IngestionMappingReference | Name der zu verwendenden vorhandenen [Erfassungszuordnung](kusto/management/create-ingestion-mapping-command.md). Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| Komprimierung | Datenkomprimierung. `None` (Standardeinstellung) oder `GZip`-Komprimierung.|
| Codierung | Datencodierung. Die Standardeinstellung ist UTF8. Alle [von .NET unterstützten Codierungen](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks) können verwendet werden. |
| Tags (Vorschauversion) | Eine Liste der [Tags](kusto/management/extents-overview.md#extent-tagging), die den erfassten Daten zugeordnet werden sollen (formatiert als JSON-Arrayzeichenfolge). Die Verwendung von Tags hat [Auswirkungen auf die Leistung](kusto/management/extents-overview.md#performance-notes-1). |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="set-events-routing"></a>Festlegen des Ereignisroutings

Beim Einrichten einer Event Hub-Verbindung mit einem Azure Data Explorer-Cluster geben Sie die Zieltabelleneigenschaften an (Tabellenname, Datenformat, Komprimierung und Zuordnung). Das Standardrouting für Ihre Daten wird auch als `static routing` bezeichnet.
Sie können mithilfe von Ereigniseigenschaften auch Zieltabelleneigenschaften für jedes Ereignis angeben. Die Verbindung leitet die Daten wie in [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) festgelegt dynamisch weiter und setzt dabei die statischen Eigenschaften für dieses Ereignis außer Kraft.

Im folgenden Beispiel werden Event Hub-Details festgelegt und Metrikdaten zum Wetter an die Tabelle `WeatherMetrics` gesendet.
Die Daten liegen im `json`-Format vor. `mapping1` ist für die Tabelle `WeatherMetrics` vordefiniert.

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

## <a name="set-event-system-properties-mapping"></a>Festlegen der Zuordnung von Ereignissystemeigenschaften

Systemeigenschaften speichern vom Event Hubs-Dienst festgelegte Eigenschaften, wenn das Ereignis in die Warteschlange eingereiht wird. Die Event Hub-Verbindung mit Azure Data Explorer bettet die ausgewählten Eigenschaften in die Daten ein, die in Ihre Tabelle gelangen.

> [!Note]
> * Systemeigenschaften werden für Ereignisse mit einem Datensatz unterstützt.
> * Systemeigenschaften werden nicht für komprimierte Daten unterstützt.
> * Für die `csv`-Zuordnung werden Eigenschaften in der in der folgenden Tabelle aufgeführten Reihenfolge am Anfang des Datensatzes hinzugefügt. Für die `json`-Zuordnung werden Eigenschaften entsprechend den Eigenschaftsnamen in der folgenden Tabelle hinzugefügt.

### <a name="event-hub-exposes-the-following-system-properties"></a>Event Hub macht die folgenden Systemeigenschaften verfügbar.

|Eigenschaft |Datentyp |Beschreibung|
|---|---|---|
| x-opt-enqueued-time |datetime | UTC-Zeit, zu der das Ereignis in die Warteschlange eingereiht wurde. |
| x-opt-sequence-number |long | Die logische Folgenummer des Ereignisses innerhalb des Partitionsdatenstroms des Event Hubs.
| x-opt-offset |Zeichenfolge | Der Offset des Ereignisses vom Event Hub-Partitionsdatenstrom. Der Offsetbezeichner ist innerhalb einer Partition des Event Hub-Datenstroms eindeutig. |
| x-opt-publisher |Zeichenfolge | Der Name des Herausgebers, wenn die Nachricht an einen Herausgeberendpunkt gesendet wurde. |
| x-opt-partition-key |Zeichenfolge |Der Partitionsschlüssel der entsprechenden Partition, in der das Ereignis gespeichert wurde. |

Wenn Sie **Ereignissystemeigenschaften** im Abschnitt **Datenquelle** der Tabelle ausgewählt haben, müssen Sie die Eigenschaften in das Tabellenschema und die Zuordnung einschließen.

### <a name="examples"></a>Beispiele

#### <a name="table-schema-example"></a>Beispiel für das Tabellenschema

Erstellen oder ändern Sie das Tabellenschema mit dem Befehl für das Tabellenschema, wenn Ihre Daten Folgendes enthalten:
* die Spalten `Timespan`, `Metric` und `Value`  
* die Eigenschaften `x-opt-enqueued-time` und `x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>Beispiel für eine CSV-Zuordnung

Führen Sie die folgenden Befehle aus, um am Anfang des Datensatzes Daten hinzuzufügen.
Eigenschaften werden in der in der obigen Tabelle aufgeführten Reihenfolge am Anfang des Datensatzes hinzugefügt.
Die Ordnungswerte sind wichtig für CSV-Zuordnungen, bei denen sich die Ordnungszahlen von Spalten basierend auf den zugeordneten Systemeigenschaften ändern.

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
 
#### <a name="json-mapping-example"></a>Beispiel für eine JSON-Zuordnung

Fügen Sie Daten anhand der Namen der Systemeigenschaften hinzu, die auf dem Blatt *Datenverbindung* der Liste *Ereignissystemeigenschaften* angegeben sind. Führen Sie folgenden Befehl aus:

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
> Die beste Leistung erzielen Sie, wenn Sie alle Ressourcen in der gleichen Region wie den Azure Data Explorer-Cluster erstellen.

### <a name="create-an-event-hub"></a>Erstellen eines Event Hubs

[Erstellen Sie einen Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create), sofern Sie noch keinen haben. Eine Vorlage finden Sie im Leitfaden zum [Erstellen eines Event Hubs](ingest-data-event-hub.md#create-an-event-hub).

> [!Note]
> * Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf.
> * Die Consumergruppe *muss* pro Consumer eindeutig sein. Erstellen Sie eine dedizierte Consumergruppe für die Azure Data Explorer-Verbindung.

#### <a name="generate-data"></a>Generieren von Daten

* Sehen Sie sich die [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) an, die Daten generiert und an einen Event Hub sendet.

Ein Ereignis kann entsprechend dem Größenlimit einen oder mehrere Datensätze enthalten. Im folgenden Beispiel werden zwei Ereignisse mit jeweils fünf angefügten Datensätzen gesendet:

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

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten aus Event Hub in Azure Data Explorer](ingest-data-event-hub.md)
* [Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit C#](data-connection-event-hub-csharp.md)
* [Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit Python](data-connection-event-hub-python.md)
* [Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit einer Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md)
