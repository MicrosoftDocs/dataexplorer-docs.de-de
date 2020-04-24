---
title: Shuffle Query-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird das Mischen von Abfragen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 600e561937b779ff9dd10d5d82f5522d204466a0
ms.sourcegitcommit: 2e63c7c668c8a6200f99f18e39c3677fcba01453
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117691"
---
# <a name="shuffle-query"></a>Abfrage mischen

Bei der shuffle-Abfrage handelt es sich um eine semantische Beibehaltung für eine Reihe von Operatoren, die eine shuffle-Strategie unterstützen, die abhängig von den tatsächlichen Daten die Leistung erheblich verbessert.

Operatoren [, die](summarizeoperator.md) die Wiederherstellung in Kusto unterstützen, sind [Join](joinoperator.md), Sum und [make-Series](make-seriesoperator.md).

Die Strategie zum Mischen von Abfragen kann durch den Abfrage `hint.strategy = shuffle` Parameter `hint.shufflekey = <key>`oder festgelegt werden.

Sie können auch die Definition einer [Richtlinie für die Daten Partitionierung](../management/partitioningpolicy.md) für Ihre Tabelle untersuchen. Abfragen, in denen `shufflekey` auch der Hash Partitions Schlüssel der Tabelle ist, werden erwartungsgemäß ausgeführt, da die Menge an Daten, die für die Umstellung auf Cluster Knoten erforderlich ist, erheblich reduziert wird.

**Syntax**

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

```kusto
T
| summarize hint.strategy = shuffle count(), avg(price) by supplier
```

```kusto
T
| make-series hint.shufflekey = Fruit PriceAvg=avg(Price) default=0  on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

Durch diese Strategie wird die Last auf allen Cluster Knoten gemeinsam genutzt, wobei jeder Knoten eine Partition der Daten verarbeitet.
Es ist hilfreich, die Strategie zum Mischen von Abfragen zu verwenden,`join` wenn der `summarize` Schlüssel ( `make-series` Schlüssel, Schlüssel oder Schlüssel) über eine hohe Kardinalität verfügt, was dazu führt, dass die reguläre Abfrage Strategie die Abfrage Limits trifft.

**Unterschied zwischen Hint. Strategy = shuffle und Hint. shufflekey = Key**

`hint.strategy=shuffle`bedeutet, dass der Operator mit gemischtes Operator von allen Schlüsseln gemischt wird.
Beispielsweise in dieser Abfrage:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Die Hash Funktion, mit der die Daten gemischt werden, verwendet die beiden Schlüssel ActivityId und ProcessID.

Die obige Abfrage ist äquivalent zu:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Dieser Hinweis kann verwendet werden, wenn Sie daran interessiert sind, die Daten mit allen Schlüsseln des Mischungs Operators zu beenden, da der Verbund Schlüssel zu eindeutig ist, aber jeder Schlüssel nicht eindeutig genug ist.
Wenn der Operator mit gemischter Operator andere, mit einem `summarize` Operator `join`wie oder verwendet, wird die Abfrage komplexer, und Hint. Strategy = shuffle wird nicht angewendet.

Zum Beispiel:

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.strategy = shuffle (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Wenn Sie in diesem Fall das `hint.strategy=shuffle` anwenden (anstatt die Strategie während der Abfrage Planung zu ignorieren) und die Daten nach dem Verbund Schlüssel [`ActivityId`, `numeric_column`] mischen, ist das Ergebnis nicht richtig.
Der `summarize` , der sich auf der linken Seite der `join` groubs durch eine Teilmenge der `join` Schlüssel befindet, die `ActivityId`ist. Dies bedeutet, dass `summarize` der nach dem Schlüssel `ActivityId` gruppiert wird, während die Daten durch den Verbund Schlüssel [`ActivityId`, `numeric_column`] partitioniert werden.
Das Herunterfahren durch den Verbund Schlüssel [`ActivityId`, `numeric_column`] bedeutet nicht, dass es sich um einen gültigen Vorgang für die Schlüssel-ActivityId handelt und die Ergebnisse möglicherweise falsch sind.

Dieses Beispiel vereinfacht dies, wenn die für einen Verbund Schlüssel verwendete Hash Funktion`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|Numericcolumn|hash_by_key|
|---|---|---|
|Activity1|2|56|
|Activity1|1|65|



Wie Sie sehen, wurde der Verbund Schlüssel für beide Datensätze den verschiedenen Partitionen 56 und 65 zugeordnet, aber diese beiden Datensätze weisen `ActivityId` denselben Wert auf. `summarize` Dies bedeutet, dass der auf `join` der linken Seite von, der erwartet `ActivityId` , dass ähnliche Werte der Spalte in derselben Partition vorhanden sind, zu falschen Ergebnissen führt.

In diesem Fall löst `hint.shufflekey` dieses Problem, indem Sie den Schlüssel zum Mischen auf dem Join `hint.shufflekey = ActivityId` angibt, zu dem es sich um einen gemeinsamen Schlüssel für alle abbrechbaren Operatoren handelt.
In diesem Fall ist die ordnungsgemäße Wiedergabe sicher `join` und `summarize` wird durch denselben Schlüssel heruntergefahren, sodass sich alle ähnlichen Werte in derselben Partition befinden, wenn die Ergebnisse korrekt sind:

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.shufflekey = ActivityId (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

|ActivityId|Numericcolumn|hash_by_key|
|---|---|---|
|Activity1|2|56|
|Activity1|1|65|

Bei der shuffle-Abfrage ist die Standard Partitionsnummer die Anzahl der Cluster Knoten. Diese Zahl kann überschrieben werden, indem die Syntax `hint.num_partitions = total_partitions` verwendet wird, mit der die Anzahl der Partitionen gesteuert wird.

Dieser Hinweis ist nützlich, wenn der Cluster über eine kleine Anzahl von Cluster Knoten verfügt, bei denen die Standard Partitionsnummer ebenfalls klein ist und die Abfrage weiterhin ausfällt oder lange Ausführungszeit benötigt.

Beachten Sie, dass durch das Festlegen von vielen Partitionen die Leistung beeinträchtigt und mehr Cluster Ressourcen beansprucht werden. Daher wird empfohlen, die Partitionsnummer sorgfältig auszuwählen (beginnend mit "Hint. Strategy = Shuffle" und der schrittweisen Erhöhung der Partitionen).

**Beispiele**

Im folgenden Beispiel wird gezeigt, `summarize` wie die Leistung von shuffle erheblich verbessert wird.

Die Quell Tabelle enthält 150 Mio. Datensätze, und die Kardinalität des Group by-Schlüssels ist 10M, der über 10 Cluster Knoten verteilt wird.

Durch Ausführen der `summarize` regulären Strategie endet die Abfrage nach 1:08, und die Auslastung der Speicherauslastung beträgt ca. 3 GB:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Anzahl|
|---|
|1086|

Bei der Verwendung `summarize` der shuffle-Strategie endet die Abfrage nach ungefähr 7 Sekunden, und die Auslastung der Speicherauslastung beträgt 0,43 GB:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Anzahl|
|---|
|1086|

Das folgende Beispiel zeigt die Verbesserung in einem Cluster mit zwei Cluster Knoten, die Tabelle 60 Mio. Datensätze und die Kardinalität des Group by-Schlüssels 2 m.

Wenn Sie die Abfrage `hint.num_partitions` ohne ausführen, werden nur 2 Partitionen (als Cluster Knotennummer) verwendet, und die folgende Abfrage dauert ~ 1:10 Minuten:

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

Wenn die Partitionsnummer auf 10 festgelegt wird, endet die Abfrage nach 23 Sekunden: 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

Im folgenden Beispiel wird gezeigt, `join` wie die Leistung von shuffle erheblich verbessert wird.

Die Beispiele wurden in einem Cluster mit 10 Knoten getestet, bei denen die Daten über alle diese Knoten verteilt sind.

Die linke Tabelle enthält 15M-Datensätze, bei denen die Kardinalität des `join` Schlüssels ~ 14m ist, die Rechte Seite `join` von 150 Mio. Datensätze und die Kardinalität des `join` Schlüssels 10 m.
Wenn die reguläre Strategie von ausgeführt `join`wird, endet die Abfrage nach ungefähr 28 Sekunden, und die Auslastung der Speicherauslastung beträgt 1,43 GB:

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Bei der Verwendung `join` der shuffle-Strategie endet die Abfrage nach ungefähr 4 Sekunden, und die Speicherauslastung beträgt 0,3 GB:

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Die gleichen Abfragen für ein größeres DataSet, bei dem die linke Seite `join` von 150 Mio. ist, und die Kardinalität des Schlüssels ist 148m, die Rechte Seite `join` von beträgt 1,5 b, und die Kardinalität des Schlüssels beträgt ungefähr 100 Mio.

Die Abfrage mit der Standard `join` Strategie trifft Kusto-Limits und ein Timeout nach 4 Minuten.
Bei der Verwendung `join` der shuffle-Strategie endet die Abfrage nach ungefähr 34 Sekunden, und die Auslastung der Speicherauslastung beträgt 1,23 GB.


Das folgende Beispiel zeigt die Verbesserung in einem Cluster mit zwei Cluster Knoten, die Tabelle 60 Mio. Datensätze und die Kardinalität des `join` Schlüssels 2 m.
Wenn Sie die Abfrage `hint.num_partitions` ohne ausführen, werden nur 2 Partitionen (als Cluster Knotennummer) verwendet, und die folgende Abfrage dauert ~ 1:10 Minuten:

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

Wenn die Partitionsnummer auf 10 festgelegt wird, endet die Abfrage nach 23 Sekunden: 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```
