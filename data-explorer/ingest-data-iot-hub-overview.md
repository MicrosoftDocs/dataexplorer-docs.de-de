---
title: 'Erfassen von Daten aus IoT Hub: Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Erfassung von Daten aus IoT Hub in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 5437a4ecb77b81e7ffd0e60dfa3bacb76240a094
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502703"
---
# <a name="create-a-connection-to-iot-hub"></a>Herstellen einer Verbindung mit IoT Hub

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) ist ein in der Cloud gehosteter, verwalteter Dienst, der als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung und den von ihr verwalteten Geräten dient. Azure Data Explorer bietet unter Verwendung des [Event Hub-kompatiblen integrierten Endpunkts](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints) eine kontinuierliche Erfassung aus kundenseitig verwalteten IoT Hub-Instanzen.

Die IoT-Erfassungspipeline umfasst mehrere Schritte. Als Erstes erstellen Sie eine IoT Hub-Instanz, bei der Sie dann ein Gerät registrieren. Anschließend erstellen Sie eine Zieltabelle in Azure Data Explorer, in der die [Daten in einem bestimmten Format](#data-format) anhand der [Erfassungseigenschaften](#set-ingestion-properties) erfasst werden. Die IoT Hub-Verbindung muss über Informationen zum [Ereignisrouting](#set-events-routing) verfügen, um eine Verbindung mit der Azure Data Explorer-Tabelle herzustellen. Daten werden mit ausgewählten Eigenschaften gemäß der [Eigenschaftenzuordnung des Ereignissystems](#set-event-system-properties-mapping) eingebettet. Dieser Prozess kann über das [Azure-Portal](ingest-data-iot-hub.md), programmgesteuert mit [C#](data-connection-iot-hub-csharp.md) oder [Python](data-connection-iot-hub-python.md) oder mit der [Azure Resource Manager-Vorlage](data-connection-iot-hub-resource-manager.md) verwaltet werden.

Allgemeine Informationen zur Datenerfassung in Azure Data Explorer finden Sie unter [Übersicht über die Datenerfassung in Azure Data Explorer](ingest-data-overview.md).

## <a name="data-format"></a>Datenformat

* Daten werden in Form von [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)-Objekten aus dem Event Hub-Endpunkt gelesen.
* Siehe [Unterstützte Formate](ingestion-supported-formats.md).
    > [!NOTE]
    > Das RAW-Format wird von IoT Hub nicht unterstützt.
* Siehe [Unterstützte Komprimierungen](ingestion-supported-formats.md#supported-data-compression-formats).
  * Die ursprüngliche Größe der nicht komprimierten Daten sollte Teil der Blobmetadaten sein. Andernfalls wird sie von Azure Data Explorer geschätzt. Das Größenlimit für die Erfassung von nicht komprimierten Daten pro Datei ist 4 GB.

## <a name="set-ingestion-properties"></a>Festlegen von Erfassungseigenschaften

Erfassungseigenschaften weisen den Erfassungsprozess an, wohin die Daten weitergeleitet und wie sie verarbeitet werden sollen. Sie können [Erfassungseigenschaften](ingestion-properties.md) der Ereignisse mithilfe von [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) angeben. Sie können die folgenden Eigenschaften festlegen:

|Eigenschaft |Beschreibung|
|---|---|
| Tabelle | Name (unter Berücksichtigung der Groß/Kleinschreibung) der vorhandenen Zieltabelle. Überschreibt das `Table`-Element, das im Bereich `Data Connection` festgelegt ist. |
| Format | Datenformat. Überschreibt das `Data format`-Element, das im Bereich `Data Connection` festgelegt ist. |
| IngestionMappingReference | Name der zu verwendenden vorhandenen [Erfassungszuordnung](kusto/management/create-ingestion-mapping-command.md). Überschreibt das `Column mapping`-Element, das im Bereich `Data Connection` festgelegt ist.|
| Codierung |  Datencodierung. Die Standardeinstellung ist UTF8. Alle [von .NET unterstützten Codierungen](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks) können verwendet werden. |

## <a name="set-events-routing"></a>Festlegen des Ereignisroutings

Beim Einrichten einer IoT Hub-Verbindung mit einem Azure Data Explorer-Cluster geben Sie die Zieltabelleneigenschaften an (Tabellenname, Datenformat und Zuordnung). Diese Einstellung ist das Standardrouting für Ihre Daten, das auch als statisches Routing bezeichnet wird.
Sie können mithilfe von Ereigniseigenschaften auch Zieltabelleneigenschaften für jedes Ereignis angeben. Die Verbindung leitet die Daten wie in [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) festgelegt dynamisch weiter und setzt dabei die statischen Eigenschaften für dieses Ereignis außer Kraft.

> [!Note]
> Wenn Sie **Meine Daten enthalten Routinginformationen** auswählen, müssen Sie die erforderlichen Routinginformationen in den Ereigniseigenschaften angeben.

## <a name="set-event-system-properties-mapping"></a>Festlegen der Zuordnung von Ereignissystemeigenschaften

Die Systemeigenschaften sind eine Sammlung, in der Eigenschaften gespeichert werden, die beim Empfang des Ereignisses vom IoT Hub-Dienst festgelegt werden. Die IoT Hub-Verbindung mit Azure Data Explorer bettet die ausgewählten Eigenschaften in die Daten ein, die in Ihre Tabelle gelangen.

> [!Note]
> Für die `csv`-Zuordnung werden Eigenschaften in der in der folgenden Tabelle aufgeführten Reihenfolge am Anfang des Datensatzes hinzugefügt. Für die `json`-Zuordnung werden Eigenschaften entsprechend den Eigenschaftsnamen in der folgenden Tabelle hinzugefügt.

### <a name="system-properties"></a>Systemeigenschaften

IoT Hub macht die folgenden Systemeigenschaften verfügbar:

|Eigenschaft |BESCHREIBUNG|
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

Wenn Sie **Ereignissystemeigenschaften** im Abschnitt **Datenquelle** der Tabelle ausgewählt haben, müssen Sie die Eigenschaften in das Tabellenschema und die Zuordnung einschließen.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="create-iot-hub-connection"></a>Erstellen einer IoT Hub-Verbindung

> [!Note]
> Die beste Leistung erzielen Sie, wenn Sie alle Ressourcen in der gleichen Region wie den Azure Data Explorer-Cluster erstellen.

### <a name="create-an-iot-hub"></a>Erstellen eines IoT Hubs

[Erstellen Sie einen IoT Hub](ingest-data-iot-hub.md#create-an-iot-hub), sofern Sie noch keinen haben. Die Verbindung mit IoT Hub kann über das [Azure-Portal](ingest-data-iot-hub.md), programmgesteuert mit [C#](data-connection-iot-hub-csharp.md) oder [Python](data-connection-iot-hub-python.md) oder mit der [Azure Resource Manager-Vorlage](data-connection-iot-hub-resource-manager.md) verwaltet werden.

> [!Note]
> * Die Anzahl von `device-to-cloud partitions` kann nicht geändert werden. Behalten Sie daher beim Festlegen der Partitionsanzahl die langfristige Skalierung im Hinterkopf.
> * Die Consumergruppe muss pro Consumer eindeutig sein. Erstellen Sie eine dedizierte Consumergruppe für die Azure Data Explorer-Verbindung. Suchen Sie im Azure-Portal nach Ihrer Ressource, und wechseln Sie zu `Built-in endpoints`, um eine neue Consumergruppe hinzuzufügen.

## <a name="sending-events"></a>Senden von Ereignissen

Weitere Informationen finden Sie im [Beispielprojekt](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) zur Simulation eines Geräts und Generierung von Daten.

## <a name="next-steps"></a>Nächste Schritte

Es gibt verschiedene Methoden zum Erfassen von Daten in IoT Hub. Unter den folgenden Links finden Sie exemplarische Vorgehensweisen für jede dieser Methoden.

* [Erfassen von Daten aus IoT Hub in Azure Data Explorer](ingest-data-iot-hub.md)
* [Erstellen einer IoT Hub-Datenverbindung für Azure Data Explorer mit C# (Vorschau)](data-connection-iot-hub-csharp.md)
* [Erstellen einer IoT Hub-Datenverbindung für Azure Data Explorer mit Python (Vorschau)](data-connection-iot-hub-python.md)
* [Erstellen einer IoT Hub-Datenverbindung für Azure Data Explorer mit einer Azure Resource Manager-Vorlage](data-connection-iot-hub-resource-manager.md)
