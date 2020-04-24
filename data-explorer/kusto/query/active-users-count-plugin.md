---
title: active_users_count-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird active_users_count-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f324507d1a4528c5efefc14f7820437383211ca6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519345"
---
# <a name="active_users_count-plugin"></a>active_users_count Plugin

Berechnet die unterschiedliche Anzahl von Werten, bei denen jeder Wert in mindestens einer Mindestanzahl von Perioden in einer Nachwartezeitperiode angezeigt wurde.

Nützlich für die Berechnung unterschiedlicher Anzahlen nur von "Fans", ohne Auftritte von "Nicht-Fans". Ein Benutzer wird nur dann als "Fan" gezählt, wenn er während des Lookback-Zeitraums aktiv war. Der Lookback-Zeitraum wird nur verwendet, `active` um zu bestimmen, ob ein Benutzer als "Fan" betrachtet wird oder nicht. Die Aggregation selbst schließt keine Benutzer aus dem Lookback-Fenster ein (im Gegensatz zu [sliding_window_counts] (sliding-window-counts-plugin.md), in dem sich die Aggregation über dem Schiebefenster der Lookback-Periode befindet).

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` *Start* `,` *End* `,` `,` *Period* `,` `,` *Bin* `,` `,` `,` *IdColumn* `,` *TimelineColumn* *dim2* *dim1* *ActivePeriodsCount* *LookbackWindow* IdColumn TimelineColumn Start End LookbackWindow Period ActivePeriodsCount Bin [ dim1 dim2 ...] `active_users_count(``)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColumn*: Der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *TimelineColumn*: Der Name der Spalte, die die Zeitachse darstellt.
* *Start*: (optional) Skalar mit Wert des Analysestartzeitraums.
* *Ende*: (optional) Skalar mit Wert der Analyseendperiode.
* *LookbackWindow*: Ein gleitendes Zeitfenster, das einen Zeitraum definiert, in dem die Benutzerdarstellung aktiviert ist. Der Lookback-Zeitraum beginnt bei ([aktuelles Erscheinungsbild] - [Lookback-Fenster]) und endet auf ([aktuelles Erscheinungsbild]). 
* *Zeitraum*: Skalarkonstante Zeitspanne als einzelne Erscheinung zu zählen (ein Benutzer wird als aktiv gezählt, wenn es in mindestens unterschiedlichen ActivePeriodsCount dieser Zeitspanne angezeigt wird.
* *ActivePeriodsCount*: Minimale Anzahl unterschiedlicher aktiver Perioden, um zu entscheiden, ob der Benutzer aktiv ist. Aktive Benutzer sind diejenigen, die mindestens (gleich oder größer als) aktive Perioden gezählt haben.
* *Bin*: Skalarkonstantenwert des Analyseschrittzeitraums. Kann entweder ein numerischer/datumszeitlicher/zeitstempel-Wert sein, oder eine Zeichenfolge, die `week` / `month` / `year`eine von ist, in diesem Fall werden alle Perioden Anfang des[Monatsbeginns](startofyearfunction.md) des[Monats](startofmonthfunction.md)/entsprechend [beginnen.](startofweekfunction.md)/
* *dim1*, *dim2*, ...: (optional) Liste der Dimensionsspalten, die die Berechnung der Aktivitätsmetriken aufteilen.

**Rückgabe**

Gibt eine Tabelle mit den unterschiedlichen Zählwerten für IDs zurück, die in der Nachwartezeit, für jede Zeitachsenperiode und für jede vorhandene Dimensionskombination in über ActivePeriodCounts angezeigt wurden.

Ausgabetabellenschema ist:

|*TimelineColumn*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|Typ: ab *TimelineColumn*|..|..|..|long|


**Beispiele**

Berechnen Sie die wöchentliche Anzahl der verschiedenen Benutzer, die in mindestens an 3 verschiedenen Tagen über einen Zeitraum von vorherigen 8 Tagen erschienen. Analysezeitraum: Juli 2018.

```kusto
let Start = datetime(2018-07-01);
let End = datetime(2018-07-31);
let LookbackWindow = 8d;
let Period = 1d;
let ActivePeriods = 3;
let Bin = 7d; 
let T =  datatable(User:string, Timestamp:datetime)
[
    "B",      datetime(2018-06-29),
    "B",      datetime(2018-06-30),
    "A",      datetime(2018-07-02),
    "B",      datetime(2018-07-04),
    "B",      datetime(2018-07-08),
    "A",      datetime(2018-07-10),
    "A",      datetime(2018-07-14),
    "A",      datetime(2018-07-17),
    "A",      datetime(2018-07-20),
    "B",      datetime(2018-07-24)
]; 
T | evaluate active_users_count(User, Timestamp, Start, End, LookbackWindow, Period, ActivePeriods, Bin)



```

|Timestamp|dcount|
|---|---|
|2018-07-01 00:00:00.0000000|1|
|2018-07-15 00:00:00.0000000|1|

Ein Benutzer gilt als aktiv, wenn er in mindestens 3 verschiedenen Tagen (Periode = 1d, ActivePeriods=3) in einem Lookback-Fenster von 8d vor dem aktuellen Erscheinungsbild (einschließlich aktueller Darstellung) gesehen wurde. In der Abbildung unten sind die einzigen Erscheinungen, die nach diesen Kriterien aktiv sind, Benutzer A auf der 7/20 und Benutzer B auf dem 7/4 (siehe Plugin-Ergebnisse oben). Beachten Sie, dass die Darstellungen von Benutzer B am 29.06.30 zwar nicht im Start-End-Zeitbereich liegen, aber für das Lookback-Fenster von Benutzer B am 7/4 enthalten sind. 

![alt text](images/queries/active-users-count.png "Active-Users-Count")