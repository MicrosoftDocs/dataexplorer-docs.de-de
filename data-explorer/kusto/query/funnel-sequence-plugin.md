---
title: funnel_sequence-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird funnel_sequence-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0543d0083fd90d5ee3e6e94cf1ee203f6cbd50d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514738"
---
# <a name="funnel_sequence-plugin"></a>funnel_sequence-Plug-In

Berechnet die unterschiedliche Anzahl von Benutzern, die eine Sequenz von Zuständen übernommen haben, und die Verteilung der vorherigen/nächsten Zustände, die zu / geführt haben, wurden von der Sequenz gefolgt. 

```kusto
T | evaluate funnel_sequence(id, datetime_column, startofday(ago(30d)), startofday(now()), 10m, 1d, state_column, dynamic(['S1', 'S2', 'S3']))
```

**Syntax**

*T* `| evaluate` `,` *Start* `,` *End* `,` *IdColumn* `,` *TimelineColumn* *Step* *Sequence* *StateColumn* *MaxSequenceStepWindow*idcolumn TimelineColumn Start End MaxSequenceStepWindow , Step , StateColumn , Sequenz `funnel_sequence(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColum*: Spaltenverweis, muss im Quellausdruck vorhanden sein
* *TimelineColumn*: Spaltenverweis, der die Zeitachse darstellt, muss im Quellausdruck vorhanden sein
* *Start*: Skalarkonstantenwert des Analysestartzeitraums
* *Ende*: Skalarkonstantenwert der Analyseendperiode
* *MaxSequenceStepWindow*: Skalarkonstantenwert der maximal zulässigen Zeitspanne zwischen 2 sequenziellen Schritten in der Sequenz
* *Schritt*: Skalarkonstantenwert der Analyseschrittperiode (bin)
* *StateColumn*: Spaltenverweis, der den Zustand darstellt, muss im Quellausdruck vorhanden sein
* *Sequenz*: ein konstantes dynamisches Array mit `StateColumn`den Sequenzwerten (Werte werden in nachgeschlagen)

**Rückgabe**

Gibt 3 Ausgabetabellen zurück, die zum Erstellen eines Sankey-Diagramms für die analysierte Sequenz nützlich sind:

* Tabelle #1 - prev-sequence-next dcount TimelineColumn: das analysierte Zeitfenster prev: der prev-Status (kann leer sein, wenn es Benutzer gibt, die nur Ereignisse für die gesuchte Sequenz hatten, aber keine Ereignisse davor). 
    next: der nächste Zustand (kann leer sein, wenn es Benutzer gab, die nur Ereignisse für die gesuchte Sequenz hatten, aber keine Ereignisse, die darauf folgten). 
    dcount: deutliche <IdColumn> Anzahl im Zeitfenster, das [prev] --> <Sequence> --> [next] übergegangen ist. 
    Samples: ein Array von <IdColumn>IDs (von ), das der Sequenz der Zeile entspricht (maximal 128 IDs werden zurückgegeben). 

* Tabelle #2 - prev-sequence dcount TimelineColumn: das analysierte Zeitfenster prev: der prev-Status (kann leer sein, wenn es Benutzer gibt, die nur Ereignisse für die gesuchte Sequenz hatten, aber keine Ereignisse davor). 
    dcount: deutliche <IdColumn> Anzahl im Zeitfenster, das [prev] --> <Sequence> --> [next] übergegangen ist. 
    Samples: ein Array von <IdColumn>IDs (von ), das der Sequenz der Zeile entspricht (maximal 128 IDs werden zurückgegeben). 

* Tabelle #3 - Sequenz-nächste dcount TimelineColumn: das analysierte Zeitfenster als nächstes: der nächste Zustand (kann leer sein, wenn es Benutzer gab, die nur Ereignisse für die gesuchte Sequenz hatten, aber keine Ereignisse, die darauf folgten). 
    dcount: deutliche <IdColumn> Anzahl im Zeitfenster, das [prev] --> <Sequence> --> [next] übergegangen ist. 
    Samples: ein Array von <IdColumn>IDs (von ), das der Sequenz der Zeile entspricht (maximal 128 IDs werden zurückgegeben). 


**Beispiele**

### <a name="exploring-storm-events"></a>Erkunden von Sturmereignissen 

Die folgende Abfrage sucht nach der Tabelle StormEvents (Wetterstatistik für 2007) und zeigt, welches Ereignis vor/nach allen Tornado-Ereignissen im Jahr 2007 passiert.

```kusto
// Looking on StormEvents statistics: 
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

Ergebnis enthält 3 Tabellen:

* Tabelle #1: Alle möglichen Varianten dessen, was vor und nach der Sequenz passiert ist. Die zweite Zeile sagt z. B., dass es 87 verschiedene Ereignisse gab, die die nächste Sequenz hatten:`Hail` -> `Tornado` -> `Hail`


|StartTime|prev|Weiter|dcount|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|||293|
|2007-01-01 00:00:00.0000000|Hagel|Hagel|87|
|2007-01-01 00:00:00.0000000|Sturm|Sturm|77|
|2007-01-01 00:00:00.0000000|Hagel|Sturm|28|
|2007-01-01 00:00:00.0000000|Hagel||28|
|2007-01-01 00:00:00.0000000||Hagel|27|
|2007-01-01 00:00:00.0000000||Sturm|25|
|2007-01-01 00:00:00.0000000|Sturm|Hagel|24|
|2007-01-01 00:00:00.0000000|Sturm||24|
|2007-01-01 00:00:00.0000000|Überschwemmung|Überschwemmung|12|
|2007-01-01 00:00:00.0000000|Sturm|Überschwemmung|8|
|2007-01-01 00:00:00.0000000|Überschwemmung||8|
|2007-01-01 00:00:00.0000000|Trichterwolke|Sturm|6|
|2007-01-01 00:00:00.0000000||Trichterwolke|6|
|2007-01-01 00:00:00.0000000||Überschwemmung|6|
|2007-01-01 00:00:00.0000000|Trichterwolke|Trichterwolke|6|
|2007-01-01 00:00:00.0000000|Hagel|Überschwemmung|4|
|2007-01-01 00:00:00.0000000|Überschwemmung|Sturm|4|
|2007-01-01 00:00:00.0000000|Hagel|Trichterwolke|4|
|2007-01-01 00:00:00.0000000|Trichterwolke|Hagel|4|
|2007-01-01 00:00:00.0000000|Trichterwolke||4|
|2007-01-01 00:00:00.0000000|Sturm|Trichterwolke|3|
|2007-01-01 00:00:00.0000000|Heavy Rain|Sturm|2|
|2007-01-01 00:00:00.0000000|Überschwemmung|Trichterwolke|2|
|2007-01-01 00:00:00.0000000|Überschwemmung|Hagel|2|
|2007-01-01 00:00:00.0000000|Starker Wind|Sturm|1|
|2007-01-01 00:00:00.0000000|Heavy Rain|Überschwemmung|1|
|2007-01-01 00:00:00.0000000|Heavy Rain|Hagel|1|
|2007-01-01 00:00:00.0000000|Hagel|Hochwasser|1|
|2007-01-01 00:00:00.0000000|Blitz|Hagel|1|
|2007-01-01 00:00:00.0000000|Heavy Rain|Blitz|1|
|2007-01-01 00:00:00.0000000|Trichterwolke|Heavy Rain|1|
|2007-01-01 00:00:00.0000000|Überschwemmung|Hochwasser|1|
|2007-01-01 00:00:00.0000000|Hochwasser|Überschwemmung|1|
|2007-01-01 00:00:00.0000000||Heavy Rain|1|
|2007-01-01 00:00:00.0000000|Trichterwolke|Blitz|1|
|2007-01-01 00:00:00.0000000|Blitz|Sturm|1|
|2007-01-01 00:00:00.0000000|Hochwasser|Sturm|1|
|2007-01-01 00:00:00.0000000|Hagel|Blitz|1|
|2007-01-01 00:00:00.0000000||Blitz|1|
|2007-01-01 00:00:00.0000000|Tropischer Sturm|Hurrikan (Typhoon)|1|
|2007-01-01 00:00:00.0000000|Küstenflut||1|
|2007-01-01 00:00:00.0000000|Rip Current||1|
|2007-01-01 00:00:00.0000000|Schwerer Schnee||1|
|2007-01-01 00:00:00.0000000|Starker Wind||1|

* Tabelle #2: Zeigen Sie alle unterschiedlichen Ereignisse an, die nach vorherigem Ereignis gruppiert sind. Die zweite Zeile zeigt z. B., `Hail` dass es insgesamt 150 Ereignisse gab, die kurz vor`Tornado`

|StartTime|prev|Dcount|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||331|
|2007-01-01 00:00:00.0000000|Hagel|150|
|2007-01-01 00:00:00.0000000|Sturm|135|
|2007-01-01 00:00:00.0000000|Überschwemmung|28|
|2007-01-01 00:00:00.0000000|Trichterwolke|22|
|2007-01-01 00:00:00.0000000|Heavy Rain|5|
|2007-01-01 00:00:00.0000000|Hochwasser|2|
|2007-01-01 00:00:00.0000000|Blitz|2|
|2007-01-01 00:00:00.0000000|Starker Wind|2|
|2007-01-01 00:00:00.0000000|Schwerer Schnee|1|
|2007-01-01 00:00:00.0000000|Rip Current|1|
|2007-01-01 00:00:00.0000000|Küstenflut|1|
|2007-01-01 00:00:00.0000000|Tropischer Sturm|1|

* Tabelle #3: Zeigen Sie alle unterschiedlichen Ereignisse an, die nach dem nächsten Ereignis gruppiert sind. Die zweite Zeile zeigt z. B., `Hail` dass es insgesamt 143 Ereignisse gab, die nach`Tornado`

|StartTime|Weiter|Dcount|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||332|
|2007-01-01 00:00:00.0000000|Hagel|145|
|2007-01-01 00:00:00.0000000|Sturm|143|
|2007-01-01 00:00:00.0000000|Überschwemmung|32|
|2007-01-01 00:00:00.0000000|Trichterwolke|21|
|2007-01-01 00:00:00.0000000|Blitz|4|
|2007-01-01 00:00:00.0000000|Heavy Rain|2|
|2007-01-01 00:00:00.0000000|Hochwasser|2|
|2007-01-01 00:00:00.0000000|Hurrikan (Typhoon)|1|

Lassen Sie uns nun herausfinden, wie die nächste Sequenz fortgesetzt wird:  
`Hail` -> `Tornado` -> `Thunderstorm Wind`

```kusto
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, 
dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']))
```

Überspringen `Table #1` und `Table #2`, und `Table #3` schauen sie zu `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` - wir können schlussfolgern, `Hail` dass die Sequenz in `Tornado` 92 Ereignissen mit dieser Sequenz endete, wie in 41 Ereignissen fortgesetzt wurde und auf 14 zurückgedreht wurde.

|StartTime|Weiter|Dcount|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||92|
|2007-01-01 00:00:00.0000000|Hagel|41|
|2007-01-01 00:00:00.0000000|Tornado|14|
|2007-01-01 00:00:00.0000000|Überschwemmung|11|
|2007-01-01 00:00:00.0000000|Blitz|2|
|2007-01-01 00:00:00.0000000|Heavy Rain|1|
|2007-01-01 00:00:00.0000000|Hochwasser|1|