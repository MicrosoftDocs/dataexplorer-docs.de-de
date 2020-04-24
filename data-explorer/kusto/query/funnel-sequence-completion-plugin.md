---
title: funnel_sequence_completion-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird funnel_sequence_completion-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 7f168841db2df47e4e3a192b75585a1fe4d83718
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514772"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion-Plug-In

Berechnet den Trichter abgeschlossener Sequenzschritte innerhalb des Vergleichs verschiedener Zeiträume.

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

**Syntax**

*T* `| evaluate` `,` *Start* `,` *End* `,` `,` *Sequence* `,` *Step* `,` *StateColumn* *IdColumn* `,` *TimelineColumn* *MaxSequenceStepWindows* Idcolumn TimelineColumn Start Ende StateColumn Sequenz MaxSequenceStepWindows `funnel_sequence_completion(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColum*: Spaltenverweis, muss im Quellausdruck vorhanden sein
* *TimelineColumn*: Spaltenverweis, der die Zeitachse darstellt, muss im Quellausdruck vorhanden sein
* *Start*: Skalarkonstantenwert des Analysestartzeitraums
* *Ende*: Skalarkonstantenwert der Analyseendperiode
* *Schritt*: Skalarkonstantenwert der Analyseschrittperiode (bin) 
* *StateColumn*: Spaltenverweis, der den Zustand darstellt, muss im Quellausdruck vorhanden sein
* *Sequenz*: ein konstantes dynamisches Array mit `StateColumn`den Sequenzwerten (Werte werden in nachgeschlagen)
* *MaxSequenceStepWindows*: skalare konstantedynamisches Array mit den Werten der maximal zulässigen Zeitspanne zwischen dem ersten und dem letzten sequenziellen Schritt in der Sequenz, jedes Fenster (Periode) im Array generiert ein Trichteranalyseergebnis

**Rückgabe**

Gibt eine einzelne Tabelle zurück, die zum Erstellen eines Trichterdiagramms für die analysierte Sequenz nützlich ist:

* TimelineColumn: das analysierte Zeitfenster
* `StateColumn`: der Zustand der Sequenz.
* Zeitraum: Die maximale Periode (Fenster), die für den Abschluss der Schritte in der Trichtersequenz zulässig ist, die vom ersten Schritt in der Sequenz gemessen wird. Jeder Wert in *MaxSequenceStepWindows* generiert eine Trichteranalyse mit einem separaten Punkt. 
* dcount: Deutliche `IdColumn` Anzahl von im Zeitfenster, das vom `StateColumn`status ersten Sequenz auf den Wert von übergegangen ist.

**Beispiele**

### <a name="exploring-storm-events"></a>Erkunden von Sturmereignissen 

Die folgende Abfrage überprüft den Abschlusstrichter der `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` Sequenz: in "Gesamtzeit" von 1 Stunde, 4 Stunden, 1 Tag. 

```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|StartTime|EventType|Zeitraum|dcount|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|Hagel|01:00:00|2877|
|2007-01-01 00:00:00.0000000|Tornado|01:00:00|208|
|2007-01-01 00:00:00.0000000|Sturm|01:00:00|87|
|2007-01-01 00:00:00.0000000|Hagel|04:00:00|2877|
|2007-01-01 00:00:00.0000000|Tornado|04:00:00|231|
|2007-01-01 00:00:00.0000000|Sturm|04:00:00|141|
|2007-01-01 00:00:00.0000000|Hagel|1.00:00:00|2877|
|2007-01-01 00:00:00.0000000|Tornado|1.00:00:00|244|
|2007-01-01 00:00:00.0000000|Sturm|1.00:00:00|155|

Verstehen der Ergebnisse:  
Das Ergebnis es 3 Trichter (für Zeiträume: 1 Stunde, 4 Stunden und 1 Tag), während für jeden Trichterschritt eine Reihe von unterschiedlichen Anzahl von EpisodeId angezeigt wird. Sie können sehen, dass je mehr Zeit `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` gegeben wird, um die gesamte Sequenz des höheren `dcount` Werts abzuschließen (d. h. mehr Vorkommen der Sequenz, die den Schritt des Trichters erreicht).
