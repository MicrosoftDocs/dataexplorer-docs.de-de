---
title: new_activity_metrics-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird new_activity_metrics-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0aad1c91fec6855030544596a08818b80bbf3d18
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512205"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics-Plug-In

Berechnet nützliche Aktivitätsmetriken (unterschiedliche Zählwerte, unterschiedliche Anzahl neuer Werte, Aufbewahrungsrate und `New Users`Abwanderungsrate) für die Kohorte von . Jede Kohorte `New Users` (alle Benutzer, die im Zeitfenster an den ersten Platz standen) wird mit allen vorherigen Kohorten verglichen. Der Vergleich berücksichtigt *alle* vorherigen Zeitfenster. Im Datensatz für from=T2 und to=T3 sind z. B. alle Benutzer in T3, die in T1 und T2 nicht gesehen wurden, die eindeutig an der Anzahl der Benutzer. 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Syntax**

*T* `| evaluate` `,` *Start* `,` *End* `,` *Window* `,` `,` `,` `,` *IdColumn* `,` *TimelineColumn* *Cohort* *dim2* *dim1* IdColumn TimelineColumn Start End Window [ Kohorte ] [ dim1 dim2 ...] `new_activity_metrics(` [`,` *Rückblick*]`)`

**Argumente**

* *T*: Der tabellarische Eingabeausdruck.
* *IdColumn*: Der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *TimelineColumn*: Der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit Wert des Analysestartzeitraums.
* *Ende*: Skalar mit Wert der Analyseendperiode.
* *Fenster*: Skalar mit Wert des Analysefensterzeitraums. Kann entweder ein numerischer/datumszeitlicher/zeitstempel-Wert sein, oder eine Zeichenfolge, die `week` / `month` / `year`eine von ist, in diesem Fall werden alle Perioden Anfang des[Monatsbeginns](startofyearfunction.md) des[Monats](startofmonthfunction.md)/entsprechend [beginnen.](startofweekfunction.md)/ 
* *Kohorte*: (optional) eine Skalarkonstante, die eine bestimmte Kohorte angibt. Sofern nicht angegeben, werden alle Kohorten berechnet und zurückgegeben, die dem Zeitfenster der Analyse entsprechen.
* *dim1*, *dim2*, ...: (optional) Liste der Dimensionsspalten, die die Berechnung der Aktivitätsmetriken aufteilen.
* *Lookback*: (optional) ein tabellarischer Ausdruck mit einer Reihe von IDs, die zum "Look back"-Zeitraum gehören

**Rückgabe**

Gibt eine Tabelle zurück, die die unterschiedlichen Zählwerte, die unterschiedliche Anzahl neuer Werte, die Aufbewahrungsrate und die Abwanderungsrate für jede Kombination von Zeitachsenperioden von 'von' und 'bis' und für jede vorhandene Dimensionskombination aufweist.

Ausgabetabellenschema ist:

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|Typ: ab *TimelineColumn*|identisch|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`- die Kohorte neuer Nutzer. Metriken in diesem Datensatz beziehen sich auf alle Benutzer, die in diesem Zeitraum zum ersten Mal gesehen wurden. Bei der Erstentscheidung *werden* alle früheren Perioden des Analysezeitraums berücksichtigt. 
* `to_TimelineColumn`- der Zeitraum, mit dem verglichen wird. 
* `dcount_new_values`- die Zahl der `to_TimelineColumn` verschiedenen Benutzer, in denen nicht `from_TimelineColumn`in *allen* Perioden vor und einschließlich gesehen wurden. 
* `dcount_retained_values`- von allen neuen Benutzern, `from_TimelineColumn`zuerst gesehen in , die `to_TimelineCoumn`Anzahl der verschiedenen Benutzer, die in gesehen wurden .
* `dcount_churn_values`- von allen neuen Benutzern, `from_TimelineColumn`zuerst in gesehen, die Anzahl `to_TimelineCoumn`der verschiedenen Benutzer, die *nicht* in gesehen wurden.
* `retention_rate`- der `dcount_retained_values` Prozentsatz der aus der Kohorte `from_TimelineColumn`(Benutzer zuerst in gesehen ).
* `churn_rate`- der `dcount_churn_values` Prozentsatz der aus der Kohorte `from_TimelineColumn`(Benutzer zuerst in gesehen ).

**Hinweise**

Definitionen von `Retention Rate` `Churn Rate` und - siehe **Hinweise** Abschnitt in [activity_metrics Plugin-Dokumentation.](./activity-metrics-plugin.md)


**Beispiele**

Der folgende Beispieldatensatz zeigt, welche Benutzer an welchen Tagen gesehen haben. Die Tabelle wurde wie `Users` folgt auf der Grundlage einer Quelltabelle generiert: 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0,2,3,4]|
|2019-11-02 00:00:00.0000000|[0,1,3,4,5]|
|2019-11-03 00:00:00.0000000|[0,2,4,5]|
|2019-11-04 00:00:00.0000000|[0,1,2,3]|
|2019-11-05 00:00:00.0000000|[0,1,2,3,4]|

Die Ausgabe des Plugins für die Originaltabelle ist die folgende: 

```kusto
let StartDate = datetime(2019-11-01 00:00:00);
let EndDate = datetime(2019-11-07 00:00:00);
Users 
| evaluate new_activity_metrics(user, Timestamp, StartDate, EndDate-1tick, 1d) 
| where from_Timestamp < datetime(2019-11-03 00:00:00.0000000)
```

|R|from_Timestamp|to_Timestamp|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|
|---|---|---|---|---|---|---|---|
|1|2019-11-01 00:00:00.0000000|2019-11-01 00:00:00.0000000|4|4|0|1|0|
|2|2019-11-01 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|3|1|0,75|0,25|
|3|2019-11-01 00:00:00.0000000|2019-11-03 00:00:00.0000000|1|3|1|0,75|0,25|
|4|2019-11-01 00:00:00.0000000|2019-11-04 00:00:00.0000000|1|3|1|0,75|0,25|
|5|2019-11-01 00:00:00.0000000|2019-11-05 00:00:00.0000000|1|4|0|1|0|
|6|2019-11-01 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|4|0|1|
|7|2019-11-02 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|2|0|1|0|
|8|2019-11-02 00:00:00.0000000|2019-11-03 00:00:00.0000000|0|1|1|0.5|0.5|
|9|2019-11-02 00:00:00.0000000|2019-11-04 00:00:00.0000000|0|1|1|0.5|0.5|
|10|2019-11-02 00:00:00.0000000|2019-11-05 00:00:00.0000000|0|1|1|0.5|0.5|
|11|2019-11-02 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|2|0|1|

Im Folgenden finden Sie eine Analyse einiger Datensätze aus der Ausgabe: 
* Datensatz `R=3` `from_TimelineColumn`  =  `2019-11-01`, `to_TimelineColumn`  = , `2019-11-03`:
    * Die Benutzer, die für diesen Datensatz berücksichtigt werden, sind alle neuen Benutzer, die am 11.1. Da dies die erste Periode ist, sind dies alles Benutzer in diesem Bin – [0,2,3,4]
    * `dcount_new_values`– die Anzahl der Benutzer am 11/3, die am 11.1. nicht gesehen wurden. Dazu gehört auch `5`ein einzelner Benutzer – . 
    * `dcount_retained_values`– Von allen neuen Benutzern am 11.1. wie viele wurden bis zum 11/3 beibehalten? Es gibt`[0,2,4]`drei `count_churn_values` ( ), während`3`es eins ist (user= ). 
    * `retention_rate`= 0,75 – die drei behielten Benutzer von den vier neuen Nutzern, die zum ersten Mal in 11/1 gesehen wurden. 

* Datensatz `R=9` `from_TimelineColumn`  =  `2019-11-02`, `to_TimelineColumn`  = , `2019-11-04`:
    * Dieser Datensatz konzentriert sich auf die neuen Benutzer, die `1` `5`zum ersten Mal auf 11/2 gesehen wurden – Benutzer und . 
    * `dcount_new_values`– die Anzahl der Benutzer auf 11/4, `T0 .. from_Timestamp`die nicht durch alle Zeiträume gesehen wurden . Das heißt, Benutzer, die auf 11/4 gesehen werden, aber weder auf 11/1 oder 11/2 gesehen wurden – es gibt keine solchen Benutzer. 
    * `dcount_retained_values`– von allen neuen Benutzern am`[1,5]`11.2 . ), wie viele wurden bis 11/4 beibehalten? Es gibt einen solchen`[1]`Benutzer ( ), `5`während count_churn_values eins (Benutzer) ist. 
    * `retention_rate`ist 0.5 – der einzelne Benutzer, der am 11/4 von den beiden neuen am 11/2 beibehalten wurde. 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>Wöchentliche Retentionsrate und Abwanderungsrate (einzelne Woche)

Die nächste Abfrage berechnet eine Aufbewahrungs- und Abwanderungsrate für das Wochenfenster für `New Users` Kohorten (Benutzer, die in der ersten Woche angekommen sind).

```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.544632768361582|0.455367231638418|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.031638418079096|0.968361581920904|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>Wöchentliche Retentionsrate und Abwanderungsrate (vollständige Matrix)

Die nächste Abfrage berechnet die Aufbewahrungs- und Abchurzrate für das Wochenfenster für `New Users` Kohorten. Wenn das vorherige Beispiel die Statistiken für eine einzelne Woche berechnet hat, wird im Folgenden eine NxN-Tabelle für jede Von/Zu-Kombination erstellt.

```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Last parameter is omitted - 
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.190397350993377|0.809602649006622|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-08 00:00:00.0000000|2017-05-08 00:00:00.0000000|1|0|
|2017-05-08 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.405263157894737|0.594736842105263|
|2017-05-08 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.227631578947368|0.772368421052632|
|2017-05-08 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-15 00:00:00.0000000|2017-05-15 00:00:00.0000000|1|0|
|2017-05-15 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.785488958990536|0.214511041009464|
|2017-05-15 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.237644584647739|0.762355415352261|
|2017-05-22 00:00:00.0000000|2017-05-22 00:00:00.0000000|1|0|
|2017-05-22 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.621835443037975|0.378164556962025|
|2017-05-29 00:00:00.0000000|2017-05-29 00:00:00.0000000|1|0|


### <a name="weekly-retention-rate-with-lookback-period"></a>Wöchentliche Retentionsrate mit Lookback-Periode

Die folgende Abfrage berechnet die `New Users` Aufbewahrungsrate der `lookback` Kohorte unter Berücksichtigung des Zeitraums: eine tabellarische Abfrage mit einer Reihe von IDs, die verwendet werden, um die `New Users` Kohorte zu definieren (alle IDs, die nicht in diesem Satz angezeigt werden, sind `New Users`). Die Abfrage untersucht das Aufbewahrungsverhalten der `New Users` während des Analysezeitraums.

```kusto
// Generate random data of user activities
let _lookback = datetime(2017-02-01);
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
let _data = range Day from _lookback to _end  step 1d
| extend d = tolong((Day - _lookback)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000;
//
let lookback_data = _data | where Day < _start | project Day, id;
_data
| evaluate new_activity_metrics(id, Day, _start, _end, 7d, _start, lookback_data)
| project from_Day, to_Day, retention_rate
```

|from_Day|to_Day|retention_rate|
|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.404081632653061|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.257142857142857|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.296326530612245|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.0587755102040816|
