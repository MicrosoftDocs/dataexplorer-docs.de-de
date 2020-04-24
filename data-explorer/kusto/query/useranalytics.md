---
title: Benutzeranalyse - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Benutzeranalysen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/17/2019
ms.openlocfilehash: 37b5962b9441c3eb7362a239404189e489b1c3ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504980"
---
# <a name="user-analytics"></a>Benutzeranalyse

In diesem Abschnitt werden Kusto-Erweiterungen (Plugins) für User Analytics-Szenarien beschrieben.

|Szenario|Plug-In|Details|Benutzererfahrung|
|--------|------|--------|-------|
| Zählen neuer Benutzer im Laufe der Zeit | [activity_counts_metrics](activity-counts-metrics-plugin.md)|Gibt Counts/Dcounts/New Counts für jedes Zeitfenster zurück. Jedes Zeitfenster wird mit *allen* vorherigen Zeitfenstern verglichen|Kusto.Explorer: Berichtsgalerie|
| Zeitraum über Denkzeitraum: Aufbewahrungsrate/Abwanderungsrate und neue Benutzer | [activity_metrics](activity-metrics-plugin.md)|Gibt Dcounts, Aufbewahrungsrate/Curnrate für jedes Zeitfenster zurück. Jedes Zeitfenster wird mit dem *vorherigen* Zeitfenster verglichen|Kusto.Explorer: Berichtsgalerie|
| Benutzer zählen und dcounts über Schiebefenster | [sliding_window_counts](sliding-window-counts-plugin.md)|Für jedes Zeitfenster werden die Anzahl- und Wertzählungen über einen Lookback-Zeitraum in einem gleitenden Fenster-Manier angezeigt.|
| New-User-Kohorte: Aufbewahrungsrate/Abwanderungsrate und neue Benutzer | [new_activity_metrics](new-activity-metrics-plugin.md)|Vergleicht zwischen Kohorten neuer Benutzer (alle Benutzer, die im Zeitfenster an den ersten Platz gesehen wurden). Jede Kohorte wird mit allen früheren Kohorten verglichen. Vergleich berücksichtigt *alle* bisherigen Zeitfenster|Kusto.Explorer: Berichtsgalerie|
|Aktive Benutzer: unterschiedliche Zählungen |[active_users_count](active-users-count-plugin.md)|Gibt für jedes Zeitfenster unterschiedliche Benutzer zurück, wobei ein Benutzer nur berücksichtigt wird, wenn er in mindestens X-verschiedenen Zeiträumen in einem spcifizierten Lookback-Zeitraum angezeigt wird.|
|Benutzerbindung: DAU/WAU/MAU|[activity_engagement](activity-engagement-plugin.md)|Vergleicht zwischen einem inneren Zeitfenster (z.B. täglich) und einem äußeren (z.B. wöchentlich) für Recheninteraktion (z.B. DAU/WAU)|Kusto.Explorer: Berichtsgalerie|
|Sitzungen: Aktive Sitzungen zählen|[session_count](session-count-plugin.md)|Zählt Sitzungen, bei denen eine Sitzung durch einen Zeitraum definiert wird - ein Benutzerdatensatz wird als neue Sitzung betrachtet, wenn er im Nachwartezeitzeitraum aus dem aktuellen Datensatz nicht angezeigt wurde|
||||
|Trichter: vorherige und nächste Zustandssequenzanalyse | [funnel_sequence](funnel-sequence-plugin.md)|Zählt verschiedene Benutzer, die eine Folge von Ereignissen genommen haben, und die prev/next-Ereignisse, die geführt / gefolgt von der Sequenz. Nützlich für die Erstellung von [Sankey-Diagrammen](https://en.wikipedia.org/wiki/Sankey_diagram)||
|Trichter: Sequenzvervollständigungsanalyse|[funnel_sequence_completion](funnel-sequence-completion-plugin.md)|Berechnet die unterschiedliche Anzahl von Benutzern, die in jedem Zeitfenster eine angegebene Sequenz *abgeschlossen* haben|
||||