---
title: 'new_activity_metrics-Plug-in: Azure Daten-Explorer'
description: In diesem Artikel wird new_activity_metrics-Plug-in in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 447191158ce3de69c4429b5af4a33d24150af77c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248846"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics-Plug-In

Berechnet nützliche Aktivitäts Metriken (eindeutige Anzahl Werte, unterschiedliche Anzahl neuer Werte, Beibehaltungs Rate und Änderungs Rate) für die Kohorte von `New Users` . Jede Kohorte von `New Users` (alle Benutzer, die im Zeitfenster als 1 angezeigt werden) werden mit allen vorherigen Kohorten verglichen. Beim Vergleich werden *alle* vorherigen Zeitfenster berücksichtigt. Im Datensatz für from = T2 und to = T3 werden z. b. alle Benutzer in T3, die nicht in T1 und T2 angezeigt wurden, unterschiedlich gezählt. 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Syntax

*T* `| evaluate` `new_activity_metrics(` *idColumn* `,` *timelinecolenn* `,` *Start* `,` *End* `,` *Endfenster* [ `,` *Kohorte*] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *Lookback*]`)`

## <a name="arguments"></a>Argumente

* *T*: der tabellarische Eingabe Ausdruck.
* *IdColumn*: der Name der Spalte mit ID-Werten, die die Benutzeraktivität darstellen. 
* *Timelinecolren*: der Name der Spalte, die die Zeitachse darstellt.
* *Start*: Skalar mit dem Wert des Start Zeitraums der Analyse.
* *End*: Skalar mit dem Wert des endzeitraums der Analyse.
* *Fenster*: Skalar mit dem Wert des Analysefenster Zeitraums. Kann ein numerischer/DateTime-/timestamp-Wert oder eine Zeichenfolge sein, bei der es sich um einen handelt `week` / `month` / `year` . in diesem Fall werden alle Zeiträume entsprechend [starstarfweek](startofweekfunction.md) / [starttfmonth](startofmonthfunction.md) / [starttfyear](startofyearfunction.md) . 
* *Kohorte*: (optional) eine skalare Konstante, die eine bestimmte Kohorte angibt. Wenn keine Angabe erfolgt, werden alle dem Analysezeit Fenster entsprechenden Kohorten berechnet und zurückgegeben.
* *dim1*, *dim2*,...: (optional) Liste der Dimensions Spalten, in denen die Berechnung der Aktivitäts Metrik in Slice ist.
* *Lookback*: (optional) ein tabellarischer Ausdruck mit einem Satz von IDs, der zum Rückgabezeitraum gehört.

## <a name="returns"></a>Rückgabe

Gibt eine Tabelle zurück, die die unterschiedlichen Anzahl Werte, die unterschiedliche Anzahl neuer Werte, die Beibehaltungs Dauer und die Änderungs Rate für jede Kombination aus ' from '-und ' to '-Zeitachse sowie für jede vorhandene Dimensions Kombination enthält.

Das Ausgabe Tabellen Schema ist:

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|Typ: ab *timelinecolumschlag*|identisch|long|long|double|double|double|..|..|..|

* `from_TimelineColumn` -die Kohorte neuer Benutzer. Metriken in diesem Datensatz beziehen sich auf alle Benutzer, die in diesem Zeitraum zuerst gesehen wurden. Bei der Entscheidung für das *erste Mal* werden alle vorangegangenen Zeiträume im Analysezeitraum berücksichtigt. 
* `to_TimelineColumn` -der Zeitraum, mit dem verglichen wird. 
* `dcount_new_values` : die Anzahl der unterschiedlichen Benutzer `to_TimelineColumn` , in denen nicht in *allen* Perioden vor und einschließlich angezeigt wurde `from_TimelineColumn` . 
* `dcount_retained_values` -von allen neuen Benutzern, zuerst in `from_TimelineColumn` , die Anzahl der unterschiedlichen Benutzer, die in gesehen wurden `to_TimelineCoumn` .
* `dcount_churn_values` -von allen neuen Benutzern, zuerst in `from_TimelineColumn` , die Anzahl der unterschiedlichen Benutzer, die in *nicht* angezeigt wurden `to_TimelineCoumn` .
* `retention_rate` : der Prozentsatz von `dcount_retained_values` aus der Kohorte (in den ersten Benutzern `from_TimelineColumn` ).
* `churn_rate` : der Prozentsatz von `dcount_churn_values` aus der Kohorte (in den ersten Benutzern `from_TimelineColumn` ).

**Notizen**

Definitionen von `Retention Rate` und `Churn Rate` finden Sie im Abschnitt " **Hinweise** " in [activity_metrics-Plug](./activity-metrics-plugin.md) -in-Dokumentation.


## <a name="examples"></a>Beispiele

Das folgende Beispiel DataSet zeigt, welche Benutzer an welchen Tagen angezeigt werden. Die Tabelle wurde wie folgt auf Grundlage einer Quell `Users` Tabelle generiert: 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0, 2, 3, 4]|
|2019-11-02 00:00:00.0000000|[0, 1, 3, 4, 5]|
|2019-11-03 00:00:00.0000000|[0, 2, 4, 5]|
|2019-11-04 00:00:00.0000000|[0, 1, 2, 3]|
|2019-11-05 00:00:00.0000000|[0, 1, 2, 3, 4]|

Die Ausgabe des Plug-Ins für die ursprüngliche Tabelle lautet wie folgt: 

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

Im folgenden finden Sie eine Analyse einiger Datensätze aus der Ausgabe: 
* Datensatz `R=3` , `from_TimelineColumn`  =  `2019-11-01` , `to_TimelineColumn`  =  `2019-11-03` :
    * Die für diesen Datensatz berücksichtigten Benutzer sind alle neuen Benutzer, die auf 11/1 angezeigt werden. Da dies der erste Zeitraum ist, sind dies alle Benutzer in dieser bin – [0, 2, 3, 4]
    * `dcount_new_values` – die Anzahl der Benutzer auf 11/3, die auf 11/1 nicht angezeigt wurden. Dies schließt eine einzelne Benutzer – ein `5` . 
    * `dcount_retained_values` – von allen neuen Benutzern auf 11/1, wie viele wurden bis 11/3 aufbewahrt? Es gibt drei ( `[0,2,4]` ), während gleich `count_churn_values` 1 ist (User = `3` ). 
    * `retention_rate` = 0,75 – die drei Benutzer, die von den vier neuen Benutzern, die zuerst in 11/1 waren, aufbewahrt wurden. 

* Datensatz `R=9` , `from_TimelineColumn`  =  `2019-11-02` , `to_TimelineColumn`  =  `2019-11-04` :
    * Dieser Datensatz konzentriert sich auf die neuen Benutzer, die zuerst auf 11/2 –-Benutzern und angezeigt wurden `1` `5` . 
    * `dcount_new_values` – die Anzahl der Benutzer auf 11/4, die nicht durch alle Zeiträume gesehen wurden `T0 .. from_Timestamp` . Dies bedeutet, dass Benutzer, die auf 11/4 angezeigt werden, aber weder in 11/1 noch 11/2 angezeigt wurden – es gibt keine solchen Benutzer. 
    * `dcount_retained_values` – von allen neuen Benutzern auf 11/2 ( `[1,5]` ), wie viele wurden bis 11/4 aufbewahrt? Es gibt einen solchen Benutzer ( `[1]` ), während count_churn_values einer (Benutzer `5` ) ist. 
    * `retention_rate` ist 0,5 – der einzelne Benutzer, der auf 11/4 aus den beiden neuen auf 11/2 beibehalten wurde. 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>Wöchentliche Aufbewahrungs Rate und Änderungs Rate (einzelne Woche)

Die nächste Abfrage berechnet eine Beibehaltungs Dauer und Änderungs Rate für das Woche-über-Woche-Fenster der `New Users` Kohorte (Benutzer, die in der ersten Woche eingetroffen sind).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>Wöchentliche Aufbewahrungs Rate und Änderungs Rate (Complete Matrix)

Die nächste Abfrage berechnet die Beibehaltungs Dauer und die Änderungs Rate für eine Woche-über-Woche-Zeitfenster für `New Users` Kohorte. Wenn im vorherigen Beispiel die Statistik für eine einzelne Woche berechnet wurde, wird im folgenden eine NxN-Tabelle für jede from/to-Kombination erzeugt.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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


### <a name="weekly-retention-rate-with-lookback-period"></a>Wöchentliche Beibehaltungs Rate mit dem Nachschlage Zeitraum

Mit der folgenden Abfrage wird die Beibehaltungs Rate der `New Users` Kohorte berechnet, wenn der Zeitraum in Erwägung gezogen `lookback` wird: eine tabellarische Abfrage mit einem Satz von IDs, die zum Definieren der `New Users` Kohorte verwendet werden (alle IDs, die in diesem Satz nicht angezeigt werden `New Users` ). Die Abfrage untersucht das Beibehaltungs Verhalten von `New Users` während des Analyse Zeitraums.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
