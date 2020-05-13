---
title: Streamingaberfassungs-Richtlinie-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Richtlinie zum Streamen von Daten in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
ms.openlocfilehash: a0141482e3714ed710dbdc6fa00e3b8b396e77e6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373311"
---
# <a name="streaming-ingestion-policy"></a>Streamingerfassungsrichtlinie

## <a name="streaming-ingestion-target-scenario"></a>Szenario für streamingingserfassung

Die Streamingerfassung ist für Szenarien vorgesehen, in denen eine geringe Wartezeit mit einer Erfassungsdauer von weniger als zehn Sekunden für unterschiedliche Volumedaten benötigt wird. Sie wird verwendet, um die operative Verarbeitung von vielen Tabellen in einer oder mehreren Datenbanken zu optimieren, bei denen der Datenstrom in jeder Tabelle relativ klein ist (wenige Datensätze pro Sekunde), das Gesamtvolumen der Datenerfassung jedoch hoch (Tausende von Datensätzen pro Sekunde).

Wenn die Datenmenge mehr als 1 MB pro Sekunde und Tabelle beträgt, verwenden Sie anstelle der Streamingerfassung die klassische (Massen-)Erfassung. 

* Informationen zum Implementieren dieses Features finden Sie unter [Streaming](../../ingest-data-streaming.md)-Erfassung.
* Weitere Informationen über Befehle zum Steuern der streamingansung finden [Sie Untersteuern von Befehlen zum Verwalten der streamingerfassungs-Richtlinie](../management/streamingingestion-policy.md) .

## <a name="streaming-ingestion-policy-definition"></a>Richtlinien Definition für die streamingerfassung

Die Richtlinie für die streamingansung kann für eine Tabelle oder eine Datenbank definiert werden. Wenn Sie diese Richtlinie auf Datenbankebene definieren, werden die gleichen Einstellungen auf alle vorhandenen und zukünftigen Tabellen in der Datenbank angewendet. Wenn die Richtlinie für die streamingerfassung auf Tabellen-und Datenbankebene festgelegt ist, hat die Einstellung auf Tabellenebene Vorrang.

> [!NOTE]
> Wenn eine Tabelle die streamingerfassung nicht direkt durchführt, sondern nur über eine Update Richtlinie, muss für diese Tabelle keine Richtlinie für die streamingansung definiert werden. 

Mit der Richtlinie für die streaminganterzierung wird die maximale Anzahl von Zeilen speichern festgelegt, an die die Streamingdaten der Tabelle verteilt werden. Die Verteilung wird sowohl für die Verfügbarkeit als auch für die Datenraten Unterstützung benötigt.

## <a name="setting-the-number-of-row-stores"></a>Festlegen der Anzahl von Zeilen speichern

Die Anzahl der in der Richtlinie für die Streaminglösung festgelegten Zeilen Speicher muss definiert werden. Diese Zahl sollte auf der streamingdatenrate pro Tabelle basieren (grobe Schätzung ist ausreichend).
Die empfohlene Mindestanzahl von Zeilen speichern für jede Tabelle ist vier. Die maximal unterstützte Anzahl von Zeilen speichern beträgt 64.
Je höher der streamingdatensatz für die Tabelle ist, desto höher ist die erforderliche Anzahl von Zeilen speichern, die in der zugeordneten Richtlinie zur streamingerfassung benötigt werden.
In der folgenden Tabelle sind die empfohlenen Einstellungen für die empfohlenen Einstellungen (falls zweifelhafte Verwendung einer höheren Zahl) verwendet:

|Geschätzte Spitzen Zahl der stündlichen Streamingdaten (pro Tabelle)|Anzahl von Zeilen speichern|
|----------|------|
|< 1 GB/Stunde |4|
|1-2 GB/Stunde |4-8|
|2-3 GB/Stunde |8-12|
|3-4 GB/Stunde |12-16|
| > 4 GB/Stunde |

 Weitere Ratschläge hierzu erhalten Sie, wenn Sie ein [Support Ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) öffnen.

Für eine optimale Abfrage Wartezeit sollten Sie die oben beschriebene Empfehlung nicht wesentlich überschreiten.

> [!NOTE]
> Wenn Sie die streamingaberstellungs-Richtlinie für die Datenbank festlegen, weisen Sie die Anzahl der für die Tabelle benötigten Zeilen Speicher mit der höchsten Datenrate zu. 