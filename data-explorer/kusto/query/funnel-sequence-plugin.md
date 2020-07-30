---
title: 'funnel_sequence-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird funnel_sequence-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 67a5e05a5720c8a9290523344f793c07c47c5ed2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347917"
---
# <a name="funnel_sequence-plugin"></a>funnel_sequence-Plug-In

Berechnet die unterschiedliche Anzahl von Benutzern, die eine Sequenz von Zuständen angenommen haben, und die Verteilung Vorheriger/Nächster Zustände, die zu/gefolgt von der Sequenz geführt haben. 

```kusto
T | evaluate funnel_sequence(id, datetime_column, startofday(ago(30d)), startofday(now()), 10m, 1d, state_column, dynamic(['S1', 'S2', 'S3']))
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `funnel_sequence(` *idColumn* `,` *timelinecolenn* `,` *Start* `,` *Ende* `,` *maxsequencestepwindow*, *Step*, *statecolenumn*, *Sequence*`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *Idcolum*: Spalten Verweis muss im Quell Ausdruck vorhanden sein.
* *Timelinecolbin*: der Spalten Verweis, der die Zeitachse darstellt, muss im Quell Ausdruck vorhanden sein.
* *Start*: skalare konstanter Wert des Start Zeitraums der Analyse.
* *End*: skalare konstanter Wert des endzeitraums der Analyse.
* *Maxsequencestepwindow*: der skalare Konstante Wert der maximal zulässigen Zeitspanne zwischen zwei sequenziellen Schritten in der Sequenz.
* *Step*: skalare konstanter Wert des Analyseschritt Zeitraums (bin).
* *Statecolbin*: der Spalten Verweis, der den Zustand darstellt, muss im Quell Ausdruck vorhanden sein.
* *Sequence*: ein konstantes dynamisches Array mit den Sequenz Werten (Werte werden in gesucht `StateColumn` ).

## <a name="returns"></a>Rückgabe

Gibt drei Ausgabe Tabellen zurück, die zum Erstellen eines Sankey-Diagramms für die analysierte Sequenz nützlich sind:

* Table #1-Prev-Sequence-Next `dcount` timelinecolenumn: das analysierte Zeitfenster Prev: The Prev State (kann leer sein, wenn Benutzer vorhanden waren, die nur Ereignisse für die gesuchte Sequenz enthielten, aber keine Ereignisse vor der Anwendung). 
    Next: der nächste Zustand (kann leer sein, wenn Benutzer vorhanden waren, die nur Ereignisse für die gesuchte Sequenz enthielten, aber keine Ereignisse, die ihm folgten). 
    `dcount`: unterschiedliche Anzahl von `IdColumn` im Zeitfenster, das sich im Übergang befand `prev`  -->  `Sequence`  -->  `next` . 
    Beispiele: ein Array von IDs (von `IdColumn` ), das der Zeilen Sequenz entspricht (maximal 128 IDs werden zurückgegeben). 

* Table #2-Prev-Sequence `dcount` timelinecolenumn: das analysierte Zeitfenster Prev: der Prev State (kann leer sein, wenn Benutzer vorhanden waren, die nur Ereignisse für die gesuchte Sequenz enthielten, aber keine Ereignisse vor dieser). 
    `dcount`: unterschiedliche Anzahl von `IdColumn` im Zeitfenster, das sich im Übergang befand `prev`  -->  `Sequence`  -->  `next` . 
    Beispiele: ein Array von IDs (von `IdColumn` ), das der Zeilen Sequenz entspricht (maximal 128 IDs werden zurückgegeben). 

* Table #3-Sequence-Next `dcount` timelinecolenumn: das analysierte Zeitfenster weiter: der nächste Zustand (kann leer sein, wenn Benutzer vorhanden waren, die nur Ereignisse für die gesuchte Sequenz enthielten, aber keine Ereignisse, die ihm folgten). 
    `dcount`: unterschiedliche Anzahl von `IdColumn` im Zeitfenster, das sich im Übergang befand `prev`  -->  `Sequence`  -->  `next` .
    Beispiele: ein Array von IDs (von `IdColumn` ), das der Zeilen Sequenz entspricht (maximal 128 IDs werden zurückgegeben). 


## <a name="examples"></a>Beispiele

### <a name="exploring-storm-events"></a>Erkunden von Storm-Ereignissen 

In der folgenden Abfrage wird die Tabelle stormevents (Wetterstatistik für 2007) überprüft, und es wird angezeigt, welche Ereignisse vor/nach dem Eintreten aller Tornado-Ereignisse in 2007 aufgetreten sind.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Looking on StormEvents statistics: 
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

Das Ergebnis umfasst drei Tabellen:

* Table #1: alle möglichen Varianten der Vorgänge vor und nach der Sequenz. Die zweite Zeile bedeutet beispielsweise, dass 87 verschiedene Ereignisse mit folgender Sequenz vorhanden waren:`Hail` -> `Tornado` -> `Hail`


|`StartTime`|`prev`|`next`|`dcount`|
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
|2007-01-01 00:00:00.0000000|Trichter-Cloud|Sturm|6|
|2007-01-01 00:00:00.0000000||Trichter-Cloud|6|
|2007-01-01 00:00:00.0000000||Überschwemmung|6|
|2007-01-01 00:00:00.0000000|Trichter-Cloud|Trichter-Cloud|6|
|2007-01-01 00:00:00.0000000|Hagel|Überschwemmung|4|
|2007-01-01 00:00:00.0000000|Überschwemmung|Sturm|4|
|2007-01-01 00:00:00.0000000|Hagel|Trichter-Cloud|4|
|2007-01-01 00:00:00.0000000|Trichter-Cloud|Hagel|4|
|2007-01-01 00:00:00.0000000|Trichter-Cloud||4|
|2007-01-01 00:00:00.0000000|Sturm|Trichter-Cloud|3|
|2007-01-01 00:00:00.0000000|Starker Regen|Sturm|2|
|2007-01-01 00:00:00.0000000|Überschwemmung|Trichter-Cloud|2|
|2007-01-01 00:00:00.0000000|Überschwemmung|Hagel|2|
|2007-01-01 00:00:00.0000000|Starker Wind|Sturm|1|
|2007-01-01 00:00:00.0000000|Starker Regen|Überschwemmung|1|
|2007-01-01 00:00:00.0000000|Starker Regen|Hagel|1|
|2007-01-01 00:00:00.0000000|Hagel|Hochwasser|1|
|2007-01-01 00:00:00.0000000|Leichte|Hagel|1|
|2007-01-01 00:00:00.0000000|Starker Regen|Leichte|1|
|2007-01-01 00:00:00.0000000|Trichter-Cloud|Starker Regen|1|
|2007-01-01 00:00:00.0000000|Überschwemmung|Hochwasser|1|
|2007-01-01 00:00:00.0000000|Hochwasser|Überschwemmung|1|
|2007-01-01 00:00:00.0000000||Starker Regen|1|
|2007-01-01 00:00:00.0000000|Trichter-Cloud|Leichte|1|
|2007-01-01 00:00:00.0000000|Leichte|Sturm|1|
|2007-01-01 00:00:00.0000000|Hochwasser|Sturm|1|
|2007-01-01 00:00:00.0000000|Hagel|Leichte|1|
|2007-01-01 00:00:00.0000000||Leichte|1|
|2007-01-01 00:00:00.0000000|Tropischer Storm|Hurrikan (Taifun)|1|
|2007-01-01 00:00:00.0000000|Küsten Überflutung||1|
|2007-01-01 00:00:00.0000000|Aktuelle Rippen||1|
|2007-01-01 00:00:00.0000000|Starker Schnee||1|
|2007-01-01 00:00:00.0000000|Starker Wind||1|

* Table #2: zeigt alle unterschiedlichen Ereignisse an, die nach dem vorherigen Ereignis gruppiert sind. Die zweite Zeile zeigt z. b. an, dass insgesamt 150 Ereignisse von vorhanden waren `Hail` , die unmittelbar vor aufgetreten sind `Tornado` .

|`StartTime`|`prev`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||331|
|2007-01-01 00:00:00.0000000|Hagel|150|
|2007-01-01 00:00:00.0000000|Sturm|135|
|2007-01-01 00:00:00.0000000|Überschwemmung|28|
|2007-01-01 00:00:00.0000000|Trichter-Cloud|22|
|2007-01-01 00:00:00.0000000|Starker Regen|5|
|2007-01-01 00:00:00.0000000|Hochwasser|2|
|2007-01-01 00:00:00.0000000|Leichte|2|
|2007-01-01 00:00:00.0000000|Starker Wind|2|
|2007-01-01 00:00:00.0000000|Starker Schnee|1|
|2007-01-01 00:00:00.0000000|Aktuelle Rippen|1|
|2007-01-01 00:00:00.0000000|Küsten Überflutung|1|
|2007-01-01 00:00:00.0000000|Tropischer Storm|1|

* Table #3: zeigt alle unterschiedlichen Ereignisse an, die nach dem nächsten Ereignis gruppiert sind. Die zweite Zeile zeigt z. b. an, dass insgesamt 143 Ereignisse von `Hail` aufgetreten sind `Tornado` .

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||332|
|2007-01-01 00:00:00.0000000|Hagel|145|
|2007-01-01 00:00:00.0000000|Sturm|143|
|2007-01-01 00:00:00.0000000|Überschwemmung|32|
|2007-01-01 00:00:00.0000000|Trichter-Cloud|21|
|2007-01-01 00:00:00.0000000|Leichte|4|
|2007-01-01 00:00:00.0000000|Starker Regen|2|
|2007-01-01 00:00:00.0000000|Hochwasser|2|
|2007-01-01 00:00:00.0000000|Hurrikan (Taifun)|1|

Versuchen Sie jetzt, herauszufinden, wie die folgende Sequenz fortgesetzt wird:  
`Hail` -> `Tornado` -> `Thunderstorm Wind`

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, 
dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']))
```

`Table #1`Wenn Sie und überspringen, `Table #2` `Table #3` können wir diese Sequenz `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` in 92-Ereignissen mit dieser Sequenz beenden, als `Hail` in 41-Ereignissen fortsetzen und in 14 zurückkehren `Tornado` .

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||92|
|2007-01-01 00:00:00.0000000|Hagel|41|
|2007-01-01 00:00:00.0000000|Hüterin|14|
|2007-01-01 00:00:00.0000000|Überschwemmung|11|
|2007-01-01 00:00:00.0000000|Leichte|2|
|2007-01-01 00:00:00.0000000|Starker Regen|1|
|2007-01-01 00:00:00.0000000|Hochwasser|1|
