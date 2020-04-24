---
title: Richtlinie für die Streamingerfassung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Streamingerfassungsrichtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: ef4199e0cd8c1e14839e5508a9c6a0d793de0cc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519600"
---
# <a name="streaming-ingestion-policy"></a>Streamingerfassungsrichtlinie

## <a name="streaming-ingestion-target-scenario"></a>Streaming-Aufnahmezielszenario

Die Streamingerfassung ist für Szenarien vorgesehen, in denen eine geringe Wartezeit mit einer Erfassungsdauer von weniger als zehn Sekunden für unterschiedliche Volumedaten benötigt wird. Es wird verwendet, um die operative Verarbeitung vieler Tabellen in einer oder mehreren Datenbanken zu optimieren, in denen der Datenstrom in jede Tabelle relativ klein ist (wenige Datensätze pro Sekunde), aber das Gesamtvolumen für die Datenerfassung hoch ist (Tausende von Datensätzen pro Sekunde).

Wenn die Datenmenge mehr als 1 MB pro Sekunde und Tabelle beträgt, verwenden Sie anstelle der Streamingerfassung die klassische (Massen-)Erfassung. 

* Informationen zum Implementieren dieser Funktion finden Sie unter [Streaming-Aufnahme](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming).
* Informationen zu Streaming-Erfassungssteuerungsbefehlen finden Sie unter [Steuerungsbefehle zum Verwalten der Streaming-Aufnahmerichtlinie](../management/streamingingestion-policy.md)

## <a name="streaming-ingestion-policy-definition"></a>Definition der Streaming-Erfassungsrichtlinie

Streaming-Aufnahmerichtlinien können für eine Tabelle oder Datenbank definiert werden. Wenn Sie diese Richtlinie auf Datenbankebene definieren, werden dieselben Einstellungen auf alle vorhandenen und zukünftigen Tabellen in der Datenbank angewendet. Wenn die Streamingerfassungsrichtlinie sowohl auf Tabellen- als auch auf Datenbankebene festgelegt wird, hat die Einstellung auf Tabellenebene Vorrang.

> [!NOTE]
> Wenn eine Tabelle nicht direkt, sondern nur über eine Aktualisierungsrichtlinie, Streaming-Erfassungsrichtlinie erhält, muss in dieser Tabelle keine Streaming-Erfassungsrichtlinie definiert werden. 

Die Streamingerfassungsrichtlinie legt die maximale Anzahl von Zeilenspeichern fest, auf die die Streamingdaten der Tabelle verteilt werden. Die Verteilung wird sowohl für die Verfügbarkeits- als auch für die Datentarifunterstützung benötigt.

## <a name="setting-the-number-of-row-stores"></a>Festlegen der Anzahl der Zeilenspeicher

Die Anzahl der Zeilenspeicher, die in der Streamingerfassungsrichtlinie festgelegt sind, muss definiert werden. Diese Zahl sollte auf der Streaming-Datenrate pro Tabelle basieren (grobe Schätzung ist ausreichend).
Die empfohlene Mindestanzahl von Zeilenspeichern für eine Tabelle beträgt vier. Die maximal unterstützte Anzahl von Zeilenspeichern beträgt 64.
Je höher die Streamingdatenrate für die Tabelle, desto höher ist die erforderliche Anzahl von Zeilenspeichern, die in der zugehörigen Streamingerfassungsrichtlinie erforderlich sind.
Verwenden Sie die folgende Tabelle für die empfohlenen Einstellungen (im Zweifelsfall verwenden Sie eine höhere Anzahl):

|Geschätzte Maximale Stunden-Streaming-Datenrate (pro Tabelle)|Anzahl der Zeilenspeicher|
|----------|------|
|< 1 Gb/h |4|
|1 - 2 GB/H/ Std. |4-8|
|2 - 3 GB/H/ Std. |8-12|
|3 - 4 GB/Std. |12-16|
| > 4 GB/H/H |

 Für weitere Ratschläge hierzu öffnen Sie ein [Support-Ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

Für eine optimale Abfragelatenz sollte die Anzahl der zeilenspeicher, die pro Tabelle definiert sind, die oben genannte Empfehlung nicht wesentlich überschreiten.

> [!NOTE]
> Wenn Sie die Streamingerfassungsrichtlinie für die Datenbank festlegen, weisen Sie die Anzahl der Zeilenspeicher zu, die für die Tabelle mit der höchsten Datenrate erforderlich sind. 