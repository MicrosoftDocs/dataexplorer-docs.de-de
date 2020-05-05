---
title: Shuffle Query-Azure Daten-Explorer
description: In diesem Artikel wird das Mischen von Abfragen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bbae74ccdf0d9840a6ba4aa7427155f89c4af211
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742012"
---
# <a name="shuffle-query"></a>Abfrage mischen

Die Shuffle-Abfrage ist eine semantische Beibehaltung für eine Reihe von Operatoren, die die Shuffle-Strategie unterstützen. Abhängig von den tatsächlichen Daten kann diese Abfrage zu einer deutlich besseren Leistung führen.

Operatoren, die die Unterstützung für das herunter führen in Kusto unter [stützen, sind](summarizeoperator.md) [Join](joinoperator.md), Sum und [make-Series](make-seriesoperator.md).

Legen Sie die Abfrage Strategie "shuffle" `hint.strategy = shuffle` mithilfe `hint.shufflekey = <key>`des Abfrage Parameters oder fest.

Definieren Sie eine [Richtlinie für die Daten Partitionierung](../management/partitioningpolicy.md) in der Tabelle. 

Wird `shufflekey` als Hash Partitions Schlüssel der Tabelle festgelegt, um die Leistung zu verbessern, da die Menge an Daten, die für die Umstellung auf Cluster Knoten erforderlich ist, reduziert wird

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

Diese Strategie verwendet die Last für alle Cluster Knoten, wobei jeder Knoten eine Partition der Daten verarbeitet.
Es ist hilfreich, die Strategie zum Mischen von Abfragen zu verwenden,`join` wenn der `summarize` Schlüssel (Schlüssel `make-series` , Schlüssel oder Schlüssel) eine hohe Kardinalität aufweist und die reguläre Abfrage-Strategie auf Abfrage Limits trifft.

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

Wenn der Verbund Schlüssel zu eindeutig ist, aber jeder Schlüssel nicht eindeutig genug ist, verwenden Sie `hint` diesen, um die Daten nach allen Schlüsseln des gemischten Operators zu mischen.
Wenn der Operator mit gemischter Operator andere Operatoren mit gemischter Operator (z. b. `summarize` oder `join`) enthält, wird die Abfrage komplexer, und Hint. Strategy = shuffle wird nicht angewendet.

Beispiel:

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

Wenn Sie das `hint.strategy=shuffle` anwenden (anstatt die Strategie während der Abfrage Planung zu ignorieren) und die Daten nach dem Verbund Schlüssel [`ActivityId`, `numeric_column`] mischen, ist das Ergebnis nicht korrekt.
Der `summarize` Operator befindet sich auf der linken Seite des `join` Operators. Dieser Operator gruppiert nach einer Teilmenge der `join` Schlüssel, was in unserem Fall der Fall ist `ActivityId`. Folglich wird die `summarize` nach dem Schlüssel `ActivityId`gruppieren, während die Daten durch den Verbund Schlüssel [`ActivityId`, `numeric_column`] partitioniert werden.
Das Durchführen eines Commits durch den Verbund`ActivityId`Schlüssel `numeric_column`[,] bedeutet nicht notwendigerweise, dass die Schlüssel für `ActivityId` den Schlüssel ordnungsgemäß sind, und die Ergebnisse sind möglicherweise falsch.

In diesem Beispiel wird `binary_xor(hash(key1, 100) , hash(key2, 100))`davon ausgegangen, dass die für einen Verbund Schlüssel verwendete Hash Funktion:

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

Der Verbund Schlüssel für beide Datensätze wurde verschiedenen Partitionen (56 und 65) zugeordnet, aber diese beiden Datensätze weisen denselben Wert von `ActivityId`auf. Der `summarize` Operator auf der linken Seite von `join` erwartet, dass ähnliche Werte der Spalte `ActivityId` in derselben Partition vorhanden sind. Diese Abfrage erzeugt falsche Ergebnisse.

Sie können dieses Problem beheben, indem `hint.shufflekey` Sie verwenden, um den Schlüssel zum Mischen auf `hint.shufflekey = ActivityId`dem Join mit anzugeben. Dieser Schlüssel ist für alle Operatoren, die gemischt werden können, üblich.
Die Wiedergabe ist in diesem Fall sicher, da sowohl `join` als auch `summarize` durch denselben Schlüssel gemischt werden. Folglich werden alle ähnlichen Werte in derselben Partition angezeigt, und die Ergebnisse sind richtig:

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

Bei der shuffle-Abfrage ist die Standard Partitionsnummer die Anzahl der Cluster Knoten. Diese Zahl kann überschrieben werden, indem die- `hint.num_partitions = total_partitions`Syntax verwendet wird, mit der die Anzahl der Partitionen gesteuert wird.

Dieser Hinweis ist nützlich, wenn der Cluster über eine kleine Anzahl von Cluster Knoten verfügt, bei denen die Standard Partitionsnummer ebenfalls klein ist und die Abfrage weiterhin ausfällt oder lange Ausführungszeit benötigt.

> [!Note]
> Viele Partitionen verbrauchen möglicherweise mehr Cluster Ressourcen und beeinträchtigen die Leistung. Wählen Sie stattdessen die Partitionsnummer sorgfältig aus, indem Sie mit Hint. Strategy = shuffle beginnen und die Partitionen schrittweise erhöhen.

**Beispiele**

Im folgenden Beispiel wird gezeigt, `summarize` wie die Leistung von shuffle erheblich verbessert wird.

Die Quell Tabelle enthält 150 Mio. Datensätze, und die Kardinalität des Group by-Schlüssels beträgt 10M, der über 10 Cluster Knoten verteilt ist.

Durch Ausführen der `summarize` regulären Strategie endet die Abfrage nach 1:08, und die Speicherauslastung beträgt ungefähr 3 GB:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Anzahl|
|---|
|1086|

Bei der Verwendung `summarize` der shuffle-Strategie endet die Abfrage nach ungefähr 7 Sekunden, und die Speicherauslastung beträgt 0,43 GB:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Anzahl|
|---|
|1086|

Das folgende Beispiel zeigt die Verbesserung in einem Cluster mit zwei Cluster Knoten, die Tabelle 60 m Datensätze und die Kardinalität des Group by-Schlüssels 2 m.

Wenn Sie die Abfrage `hint.num_partitions` ohne ausführen, werden nur zwei Partitionen (als Cluster Knotennummer) verwendet, und die folgende Abfrage dauert ~ 1:10 Minuten:

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

Die linke Tabelle enthält 15M-Datensätze, bei denen die Kardinalität des `join` Schlüssels ~ 14m ist. Auf der rechten Seite von `join` sind 150 Mio. Datensätze enthalten, und die Kardinalität `join` des Schlüssels ist 10M.
Wenn die reguläre Strategie von ausgeführt `join`wird, endet die Abfrage nach ungefähr 28 Sekunden, und die Speicherauslastung beträgt 1,43 GB:

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

Die gleichen Abfragen in einem größeren Dataset, bei dem die `join` linke Seite von 150 Mio. beträgt, und die Kardinalität des Schlüssels ist 148m. Die Rechte Seite von `join` ist 1.5 b, und die Kardinalität des Schlüssels beträgt ungefähr 100 Mio.

Die Abfrage mit der Standard `join` Strategie trifft Kusto-Limits und ein Timeout nach 4 Minuten.
Bei der Verwendung `join` der shuffle-Strategie endet die Abfrage nach ungefähr 34 Sekunden, und die Speicherauslastung beträgt 1,23 GB.


Das folgende Beispiel zeigt die Verbesserung in einem Cluster mit zwei Cluster Knoten, die Tabelle 60 m Datensätze und die Kardinalität des `join` Schlüssels 2 m.
Wenn Sie die Abfrage `hint.num_partitions` ohne ausführen, werden nur zwei Partitionen (als Cluster Knotennummer) verwendet, und die folgende Abfrage dauert ~ 1:10 Minuten:

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
