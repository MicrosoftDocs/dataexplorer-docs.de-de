---
title: Von IoT Hub aufnehmen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Ingest from IoT Hub in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 33b6f4f737657ee0a6c2712f3b7cadce0c9c0a8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521351"
---
# <a name="ingest-from-iot-hub"></a>Aufnahme von IoT Hub

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) ist ein verwalteter Dienst, der in der Cloud gehostet wird und als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung und den verwalteten Geräten fungiert. Azure Data Explorer bietet kontinuierliche Erfassung von vom Kunden verwalteten IoT Hubs mithilfe des [in Endpunkt integrierten Event Hub- und](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)-integrierten Endpunkts .

## <a name="data-format"></a>Datenformat

* Daten werden vom Event Hub-Endpunkt in Form von [EventData-Objekten](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) gelesen.
* Die Ereignisnutzlast kann sich in einem der [von Azure Data Explorer unterstützten Formate](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)befinden.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Die Aufnahmeeigenschaften weisen den Aufnahmeprozess an. Wo die Daten weitergeleitet werden und wie sie verarbeitet werden. Sie können [die Aufnahmeeigenschaften](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) der Ereignisse mithilfe der [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (Groß-/Kleinschreibung) der vorhandenen Zieltabelle. Überschreibt `Table` den Satz `Data Connection` auf dem Blatt. |
| Format | Datenformat. Überschreibt `Data format` den Satz `Data Connection` auf dem Blatt. |
| IngestionMappingReference | Name der vorhandenen [Erfassungszuordnung,](../create-ingestion-mapping-command.md) die verwendet werden soll. Überschreibt `Column mapping` den Satz `Data Connection` auf dem Blatt.|
| Codieren |  Datencodierung, die Standardeinstellung ist UTF8. Es kann sich um eine von [.NET unterstützte Codierung sein.](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks) |

## <a name="events-routing"></a>Ereignisrouting

Beim Einrichten einer IoT Hub-Verbindung mit dem Azure Data Explorer-Cluster geben Sie Die Eigenschaften der Zieltabelle (Tabellenname, Datenformat und Zuordnung) an. Dies ist das Standardrouting für Ihre `static routing`Daten, auch als bezeichnet.
Sie können auch Zieltabelleneigenschaften für jedes Ereignis mithilfe von Ereigniseigenschaften angeben. Die Verbindung leitet die Daten dynamisch weiter, wie in [den EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)angegeben, und überschreibt die statischen Eigenschaften für dieses Ereignis.

## <a name="event-system-properties-mapping"></a>Zuordnung von Ereignissystemeigenschaften

Systemeigenschaften sind eine Auflistung, die zum Speichern von Eigenschaften verwendet wird, die vom IoT Hubs-Dienst zum Zeitpunkt des Empfangens des Ereignisses festgelegt werden. Die Azure Data Explorer IoT Hub-Verbindung bettet die ausgewählten Eigenschaften in die Datenlandung in der Tabelle ein.

> [!Note]
> * Für `csv` die Zuordnung werden Eigenschaften am Anfang des Datensatzes in der Reihenfolge hinzugefügt, die in der folgenden Tabelle aufgeführt ist. Für `json` die Zuordnung werden Eigenschaften entsprechend den Eigenschaftsnamen in der folgenden Tabelle hinzugefügt.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub macht die folgenden Systemeigenschaften verfügbar:

|Eigenschaft |Beschreibung|
|---|---|
| message-id | Eine vom Benutzer festgelegte Kennung für die Nachricht; wird für Anforderung-Antwort-Muster verwendet. |
| sequence-number | Eine Nummer (für jede Gerätewarteschlange eindeutig), die jeder C2D-Nachricht von IoT Hub zugewiesen wird |
| zu | Ein Ziel, das in C2D -Nachrichten angegeben wird. |
| absolute-expiry-time | Datum und Uhrzeit des Nachrichtenablaufs. |
| iothub-enqueuedtime | Datum und Uhrzeit des Empfangs der D2C-Nachricht durch IoT Hub. |
| correlation-id| Eine Zeichenfolgeneigenschaft in einer Antwortnachricht, die normalerweise die Nachrichten-ID der Anforderung im Anforderung-Antwort-Muster enthält. |
| user-id| Eine ID zum Festlegen des Ursprungs von Nachrichten. |
| iothub-ack| Ein Feedbacknachrichtengenerator. |
| iothub-connection-device-id| Eine ID, die von IoT Hub für D2C-Nachrichten festgelegt wird. Diese Eigenschaft enthält die deviceId des Geräts, das die Nachricht sendet. |
| iothub-connection-auth-generation-id| Eine ID, die von IoT Hub für D2C-Nachrichten festgelegt wird. Sie enthält die connectionDeviceGenerationId (gemäß Geräteidentitätseigenschaften) des Geräts, das die Nachricht gesendet hat. |
| iothub-connection-auth-method| Eine von IoT Hub für D2C-Nachrichten festgelegte Authentifizierungsmethode. Diese Eigenschaft enthält Informationen zu der Methode, die zum Authentifizieren des Geräts verwendet wird, das die Nachricht sendet. |

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

## <a name="create-iot-hub-connection"></a>IoT Hub-Verbindung erstellen

> [!Note]
> Um eine optimale Leistung zu erzielen, erstellen Sie alle Ressourcen in derselben Region wie der Azure Data Explorer-Cluster.

### <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Wenn Sie noch keinen haben, [erstellen Sie einen Iot Hub](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#create-an-iot-hub).

> [!Note]
> * Die `device-to-cloud partitions` Anzahl kann nicht geändert werden, daher sollten Sie beim Festlegen der Partitionsanzahl eine langfristige Skalierung berücksichtigen.
> * Die Verbrauchergruppe *muss* pro Verbraucher uniqe sein. Erstellen Sie eine Consumergruppe, die sich der Kusto-Verbindung widmet. Suchen Sie Ihre Ressource im `Built-in endpoints` Azure-Portal, und wechseln Sie zu , um eine neue Consumergruppe hinzuzufügen.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Datenerfassungsverbindung mit Azure Data Explorer

* Über Azure Portal: Verbinden Sie die [Azure Data Explorer-Tabelle](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#connect-azure-data-explorer-table-to-iot-hub)mit IoT Hub .
* Verwenden der Azure Data Explorer-Verwaltung .NET SDK: [Hinzufügen einer IoT Hub-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-csharp#add-an-iot-hub-data-connection)
* Verwenden von Azure Data Explorer-Verwaltungs-Python-SDK: [Hinzufügen einer IoT Hub-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-python#add-an-iot-hub-data-connection)
* Mit ARM-Vorlage: [Azure Resource Manager-Vorlage zum Hinzufügen einer Iot Hub-Datenverbindung](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-resource-manager#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> Wenn **Meine Daten Routinginformationen enthalten,** *müssen* Sie die erforderlichen [Routinginformationen](#events-routing) als Teil der Ereigniseigenschaften angeben.

> [!Note]
> Sobald die Verbindung festgelegt ist, werden Daten erfasst, die von Ereignissen ausgehen, die nach der Erstellungszeit in die Warteschlange eingereiht wurden.

### <a name="generating-data"></a>Generieren von Daten

* Sehen Sie sich das [Beispielprojekt an,](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) das ein Gerät simuliert und Daten generiert.