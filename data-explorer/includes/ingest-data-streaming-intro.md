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
ms.openlocfilehash: e6704592a5b0f1a984ea5651eb8073ac105fda3d
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423051"
---
Verwenden Sie die Streamingerfassung zum Laden von Daten, wenn eine geringe Wartezeit zwischen Erfassung und Abfrage erforderlich ist. Der Streamingerfassungsvorgang wird in weniger als zehn Sekunden abgeschlossen, und Ihre Daten stehen anschließend sofort zur Abfrage zur Verfügung. Diese Erfassungsmethode eignet sich für das Erfassen großer Datenmengen, etwa Tausende Datensätze pro Sekunde, die auf Tausende von Tabellen verteilt sind. Jede Tabelle empfängt eine relativ geringe Datenmenge, beispielsweise einige Datensätze pro Sekunde.

Sollte die erfasste Datenmenge mehr als 4 GB pro Stunde und Tabelle betragen, verwenden Sie anstelle der Streamingerfassung die Sammelerfassung.

Unter [Übersicht über die Datenerfassung in Azure Data Explorer](../ingest-data-overview.md) erfahren Sie mehr über verschiedene Erfassungsmethoden.
