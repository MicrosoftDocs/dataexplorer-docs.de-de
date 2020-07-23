---
title: include file
description: include file
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 1ee658d2c16bcf4174bb82d61ddc8c9b2d7d126f
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423171"
---
## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Erfassen von Daten in Ihrem Cluster mithilfe der Streamingerfassung

Zwei Streamingerfassungstypen werden unterstützt:

* [**Event Hub**](../ingest-data-event-hub.md) oder [**IoT Hub**](../ingest-data-iot-hub.md) (wird als Datenquelle verwendet).
* Für die **benutzerdefinierte Erfassung** muss eine Anwendung geschrieben werden, die eine der [Clientbibliotheken](../kusto/api/client-libraries.md) von Azure Data Explorer verwendet. Eine Beispielanwendung finden Sie [hier](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample).

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Auswählen des geeigneten Streamingerfassungstyps

|Kriterium|Event Hub|Benutzerdefinierte Erfassung|
|---------|---------|---------|
|Datenverzögerung zwischen der Initiierung der Erfassung und der Verfügbarkeit der Daten für Abfragen | Längere Verzögerung | Kürzere Verzögerung  |
|Zusätzlicher Entwicklungsaufwand | Schnelle und einfache Einrichtung, kein zusätzlicher Entwicklungsaufwand | Hoher zusätzlicher Entwicklungsaufwand für die Anwendung, um Fehler zu behandeln und die Datenkonsistenz sicherzustellen |
