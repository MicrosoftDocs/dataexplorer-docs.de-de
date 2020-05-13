---
title: Erfassung von IOT Hub-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Erfassung von IOT Hub in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c3ed0fa8608f2be5739c1143a648230f792e5d40
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373385"
---
# <a name="ingest-from-iot-hub"></a>Erfassung aus IoT Hub

[Azure IOT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) ist ein verwalteter Dienst, der in der Cloud gehostet wird und als zentraler Nachrichtenhub für bidirektionale Kommunikation zwischen Ihrer IOT-Anwendung und den verwalteten Geräten fungiert. Azure Daten-Explorer bietet fortlaufende Erfassung durch vom Kunden verwaltete IOT Hubs mithilfe seines [integrierten Event Hub-kompatiblen Endpunkts](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints).

## <a name="data-format"></a>Datenformat

* Daten werden aus dem Event Hub-Endpunkt in Form von [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) -Objekten gelesen.
* Die Ereignis Nutzlast kann in einem der Formate vorliegen, die [von Azure Daten-Explorer unterstützt](../../../ingestion-supported-formats.md)werden.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Erfassungs Eigenschaften weist den Erfassungsprozess an. Wo die Daten weitergeleitet werden und wie Sie verarbeitet werden. Mithilfe von [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)können Sie Erfassungs [Eigenschaften](../../../ingestion-properties.md) der Ereignis Erfassung angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (Groß-/Kleinschreibung beachten) der vorhandenen Ziel Tabelle. Überschreibt das `Table`, das auf dem Blatt `Data Connection` festgelegt ist. |
| Format | Datenformat. Überschreibt das `Data format`, das auf dem Blatt `Data Connection` festgelegt ist. |
| Ingestionmappingreferenzierung | Der Name der vorhandenen Erfassungs [Zuordnung](../create-ingestion-mapping-command.md) , die verwendet werden soll. Überschreibt das `Column mapping`, das auf dem Blatt `Data Connection` festgelegt ist.|
| Codieren |  Daten Codierung, der Standardwert ist UTF8. Kann eine beliebige von [.NET unterstützte Codierungen](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)sein. |

## <a name="events-routing"></a>Ereignis Routing

Beim Einrichten einer IOT Hub Verbindung mit Azure Daten-Explorer-Cluster geben Sie die Ziel Tabellen Eigenschaften an (Tabellenname, Datenformat und Zuordnung). Dies ist das Standard Routing für Ihre Daten, das auch als bezeichnet wird `static routing` .
Sie können auch die Ziel Tabellen Eigenschaften für jedes Ereignis mithilfe von Ereignis Eigenschaften angeben. Die Verbindung leitet die Daten dynamisch entsprechend der Angabe in [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)weiter und überschreibt die statischen Eigenschaften für dieses Ereignis.

## <a name="event-system-properties-mapping"></a>Zuordnung von Ereignissystemeigenschaften

System Eigenschaften sind eine Sammlung zum Speichern von Eigenschaften, die vom IOT Hubs-Dienst festgelegt werden, wenn das Ereignis empfangen wird. Die Azure-Daten-Explorer IOT Hub Verbindung bettet die ausgewählten Eigenschaften in die Daten in der Tabelle ein.

> [!Note]
> * Für die `csv` Zuordnung werden Eigenschaften am Anfang des Datensatzes in der in der folgenden Tabelle aufgeführten Reihenfolge hinzugefügt. Für `json` die Zuordnung werden Eigenschaften gemäß den Eigenschaftsnamen in der folgenden Tabelle hinzugefügt.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IOT Hub macht die folgenden Systemeigenschaften verfügbar:

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

## <a name="create-iot-hub-connection"></a>IOT Hub Verbindung erstellen

> [!Note]
> Erstellen Sie für eine optimale Leistung alle Ressourcen in der gleichen Region wie der Azure Daten-Explorer-Cluster.

### <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

Wenn Sie noch keines haben, [Erstellen Sie einen IOT Hub](../../../ingest-data-iot-hub.md#create-an-iot-hub).

> [!Note]
> * Die `device-to-cloud partitions` Anzahl kann nicht geändert werden. Daher sollten Sie die langfristige Skalierung beim Festlegen der Partitions Anzahl berücksichtigen.
> * Consumergruppe *muss* uniqe pro Consumer sein. Erstellen Sie eine consumergruppe für die Kusto-Verbindung. Suchen Sie Ihre Ressource im Azure-Portal, und navigieren Sie zu `Built-in endpoints` , um eine neue consumergruppe hinzuzufügen.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Verbindung mit der Datenerfassung mit Azure Daten-Explorer

* Über das Azure-Portal: [Verbinden Sie Azure Daten-Explorer Tabelle mit IOT Hub](../../../ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub).
* Verwenden des Azure Daten-Explorer Management .NET SDK: [Hinzufügen einer IOT Hub Datenverbindung](../../../data-connection-iot-hub-csharp.md#add-an-iot-hub-data-connection)
* Verwenden des Azure Daten-Explorer Management python SDK: [Hinzufügen einer IOT Hub Datenverbindung](../../../data-connection-iot-hub-python.md#add-an-iot-hub-data-connection)
* Mit Arm-Vorlage: [Azure Resource Manager Vorlage zum Hinzufügen einer IOT Hub-Datenverbindung](../../../data-connection-iot-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> Wenn **meine Daten Routing Informationen enthalten** , *müssen* Sie die erforderlichen [Routing](#events-routing) Informationen als Teil der Ereignis Eigenschaften bereitstellen.

> [!Note]
> Nachdem die Verbindung festgelegt wurde, erfassen Sie Daten, beginnend bei Ereignissen, die nach dem Erstellungs Zeitpunkt in die Warteschlange eingereiht wurden.

### <a name="generating-data"></a>Erstellen von Daten

* Weitere Informationen finden Sie im [Beispiel Projekt](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) , in dem ein Gerät simuliert und Daten generiert werden.