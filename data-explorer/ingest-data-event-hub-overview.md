---
title: 'Erfassen von Daten aus Event Hub: Azure Data Explorer'
description: In diesem Artikel wird die Erfassung von Daten aus Event Hub in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: b3a8c379ad010a9787fdb8b7d4e2961fb58ead9e
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502659"
---
# <a name="create-a-connection-to-event-hub"></a>Herstellen einer Verbindung mit Event Hub

Bei [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) handelt es sich um eine Big Data-Streamingplattform und einen Ereigniserfassungsdienst. Azure Data Explorer bietet eine kontinuierliche Erfassung von Daten aus kundenseitig verwalteten Event Hubs.

Die Event Hub-Erfassungspipeline überträgt Ereignisse in mehreren Schritten an Azure Data Explorer. Zunächst erstellen Sie im Azure-Portal einen Event Hub. Anschließend erstellen Sie eine Zieltabelle in Azure Data Explorer, in der die [Daten in einem bestimmten Format](#data-format) anhand der [Erfassungseigenschaften](#set-ingestion-properties) erfasst werden. Die Event Hub-Verbindung muss über Informationen zum [Ereignisrouting](#set-events-routing) verfügen. Daten werden mit ausgewählten Eigenschaften gemäß der [Eigenschaftenzuordnung des Ereignissystems](#set-event-system-properties-mapping) eingebettet. [Erstellen Sie eine Verbindung](#create-event-hub-connection) mit Event Hub, um [eine Event Hub-Instanz zu erstellen](#create-an-event-hub) und [Ereignisse](#send-events) zu senden. Dieser Prozess kann über das [Azure-Portal](ingest-data-event-hub.md), programmgesteuert mit [C#](data-connection-event-hub-csharp.md) oder [Python](data-connection-event-hub-python.md) oder mit der [Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md) verwaltet werden.

Allgemeine Informationen zur Datenerfassung in Azure Data Explorer finden Sie unter [Übersicht über die Datenerfassung in Azure Data Explorer](ingest-data-overview.md).

## <a name="data-format"></a>Datenformat

* Daten werden in Form von [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)-Objekten aus dem Event Hub gelesen.
* Siehe [Unterstützte Formate](ingestion-supported-formats.md).
    > [!NOTE]
    > Das RAW-Format wird von Event Hub nicht unterstützt.

* Siehe [Unterstützte Komprimierungen](ingestion-supported-formats.md#supported-data-compression-formats).
   * Für komprimierte Formate (Avro, Parquet, ORC) wird die Datenkomprimierung nicht unterstützt.
   * Eine benutzerdefinierte Codierung und eingebettete [Systemeigenschaften](#set-event-system-properties-mapping) werden für komprimierte Daten nicht unterstützt.
  
## <a name="set-ingestion-properties"></a>Festlegen von Erfassungseigenschaften

Erfassungseigenschaften weisen den Erfassungsprozess an, wohin die Daten weitergeleitet und wie sie verarbeitet werden sollen. Sie können [Erfassungseigenschaften](ingestion-properties.md) der Ereigniserfassung mithilfe von [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (unter Berücksichtigung der Groß/Kleinschreibung) der vorhandenen Zieltabelle. Überschreibt das `Table`-Element, das im Bereich `Data Connection` festgelegt ist. |
| Format | Datenformat. Überschreibt das `Data format`-Element, das im Bereich `Data Connection` festgelegt ist. |
| IngestionMappingReference | Name der zu verwendenden vorhandenen [Erfassungszuordnung](kusto/management/create-ingestion-mapping-command.md). Überschreibt das `Column mapping`-Element, das im Bereich `Data Connection` festgelegt ist.|
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

### <a name="system-properties"></a>Systemeigenschaften

Event Hub macht die folgenden Systemeigenschaften verfügbar:

|Eigenschaft |Datentyp |Beschreibung|
|---|---|---|
| x-opt-enqueued-time |datetime | UTC-Zeit, zu der das Ereignis in die Warteschlange eingereiht wurde. |
| x-opt-sequence-number |long | Die logische Folgenummer des Ereignisses innerhalb des Partitionsdatenstroms des Event Hubs.
| x-opt-offset |Zeichenfolge | Der Offset des Ereignisses vom Event Hub-Partitionsdatenstrom. Der Offsetbezeichner ist innerhalb einer Partition des Event Hub-Datenstroms eindeutig. |
| x-opt-publisher |Zeichenfolge | Der Name des Herausgebers, wenn die Nachricht an einen Herausgeberendpunkt gesendet wurde. |
| x-opt-partition-key |Zeichenfolge |Der Partitionsschlüssel der entsprechenden Partition, in der das Ereignis gespeichert wurde. |

Wenn Sie **Ereignissystemeigenschaften** im Abschnitt **Datenquelle** der Tabelle ausgewählt haben, müssen Sie die Eigenschaften in das Tabellenschema und die Zuordnung einschließen.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="create-event-hub-connection"></a>Herstellen einer Event Hub-Verbindung

> [!Note]
> Die beste Leistung erzielen Sie, wenn Sie alle Ressourcen in der gleichen Region wie den Azure Data Explorer-Cluster erstellen.

### <a name="create-an-event-hub"></a>Erstellen eines Event Hubs

[Erstellen Sie einen Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create), sofern Sie noch keinen haben. Die Verbindungsherstellung mit Event Hub kann über das [Azure-Portal](ingest-data-event-hub.md), programmgesteuert mit [C#](data-connection-event-hub-csharp.md) oder [Python](data-connection-event-hub-python.md) oder mit der [Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md) verwaltet werden.


> [!Note]
> * Die Partitionenanzahl kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionenanzahl die langfristige Skalierung im Hinterkopf.
> * Die Consumergruppe *muss* pro Consumer eindeutig sein. Erstellen Sie eine dedizierte Consumergruppe für die Azure Data Explorer-Verbindung.

## <a name="send-events"></a>Senden von Ereignisse

Sehen Sie sich die [Beispiel-App](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) an, die Daten generiert und an einen Event Hub sendet.

Ein Beispiel zum Generieren von Beispieldaten finden Sie unter [Erfassen von Daten aus Event Hub in Azure Data Explorer](ingest-data-event-hub.md#generate-sample-data).

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten aus Event Hub in Azure Data Explorer](ingest-data-event-hub.md)
* [Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit C#](data-connection-event-hub-csharp.md)
* [Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit Python](data-connection-event-hub-python.md)
* [Erstellen einer Event Hub-Datenverbindung für Azure Data Explorer mit einer Azure Resource Manager-Vorlage](data-connection-event-hub-resource-manager.md)
