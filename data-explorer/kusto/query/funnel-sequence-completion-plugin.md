---
title: 'funnel_sequence_completion-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird funnel_sequence_completion-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 3511d15ebf0f5e3708deeeed981a8a6808da2e48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347934"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion-Plug-In

Berechnet den Trichter der abgeschlossenen Sequenz Schritte innerhalb des Vergleichs verschiedener Zeiträume.

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `funnel_sequence_completion(` *idColumn* `,` *timelinecolenumn* `,` *Start* - `,` *End* - `,` *Schritt* `,` *statecolum* `,` *Sequence* `,` *maxsequencestepwindows*`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *Idcolum*: Spalten Verweis muss im Quell Ausdruck vorhanden sein.
* *Timelinecolbin*: der Spalten Verweis, der die Zeitachse darstellt, muss im Quell Ausdruck vorhanden sein.
* *Start*: skalare konstanter Wert des Start Zeitraums der Analyse.
* *End*: skalare konstanter Wert des endzeitraums der Analyse.
* *Step*: skalare konstanter Wert des Analyseschritt Zeitraums (bin).
* *Statecolbin*: der Spalten Verweis, der den Zustand darstellt, muss im Quell Ausdruck vorhanden sein.
* *Sequence*: ein konstantes dynamisches Array mit den Sequenz Werten (Werte werden in gesucht `StateColumn` ).
* *Maxsequencestepwindows*: Dynamisches skalare Konstante Array mit den Werten der maximal zulässigen Zeitspanne zwischen dem ersten und letzten sequenziellen Schritt in der Sequenz. Jedes Fenster (Period) im Array generiert ein Trichter Analyseergebnis.

## <a name="returns"></a>Rückgabe

Gibt eine einzelne Tabelle zurück, die zum Erstellen eines Trichter Diagramms für die analysierte Sequenz nützlich ist:

* `TimelineColumn`: das analysierte Zeitfenster
* `StateColumn`: der Zustand der Sequenz.
* `Period`: der maximale Zeitraum (Fenster), der für das Abschließen von Schritten in der Trichter Sequenz zulässig ist, gemessen aus dem ersten Schritt in der Sequenz. Jeder Wert in *maxsequencestepwindows* generiert eine Trichter Analyse mit einem separaten Zeitraum. 
* `dcount`: unterschiedliche Anzahl von `IdColumn` im Zeitfenster, die vom ersten Sequenz Zustand auf den Wert von umgestellt wurden `StateColumn` .

## <a name="examples"></a>Beispiele

### <a name="exploring-storm-events"></a>Erkunden von Storm-Ereignissen 

Mit der folgenden Abfrage wird der Abschluss Trichter der Sequenz überprüft: `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` in der Gesamtzeit von 1 Stunde, 4 Stunden, 1 Tag. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|`StartTime`|`EventType`|`Period`|`dcount`|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|Hagel|01:00:00|2877|
|2007-01-01 00:00:00.0000000|Hüterin|01:00:00|208|
|2007-01-01 00:00:00.0000000|Sturm|01:00:00|87|
|2007-01-01 00:00:00.0000000|Hagel|04:00:00|2877|
|2007-01-01 00:00:00.0000000|Hüterin|04:00:00|231|
|2007-01-01 00:00:00.0000000|Sturm|04:00:00|141|
|2007-01-01 00:00:00.0000000|Hagel|1,00:00:00|2877|
|2007-01-01 00:00:00.0000000|Hüterin|1,00:00:00|244|
|2007-01-01 00:00:00.0000000|Sturm|1,00:00:00|155|

Grundlegendes zu den Ergebnissen:  
Das Ergebnis sind drei Trichter (für Zeiträume: eine Stunde, 4 Stunden und ein Tag). Für jeden Trichter Schritt wird eine Reihe von unterschiedlichen Zählungen angezeigt. Sie können sehen, dass mehr Zeit zum Vervollständigen der gesamten Sequenz von angegeben wird `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` . der höhere `dcount` Wert wird abgerufen. Anders ausgedrückt: Es gab mehr Vorkommen der Sequenz, die den Trichter Schritt erreicht hat.
