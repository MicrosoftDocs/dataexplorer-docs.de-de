---
title: Shuffle-Abfrage - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Shuffle-Abfrage in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c687d495a41a5f73ac8dbca15d93729f2132a556
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662975"
---
# <a name="shuffle-query"></a>Shuffle-Abfrage

Shuffle-Abfrage ist eine semantisch erhaltende Transformation für eine Gruppe von Operatoren, die Shuffle-Strategie unterstützt, die je nach den tatsächlichen Daten eine erheblich bessere Leistung erzielen kann.

Operatoren, die das Mischen in Kusto unterstützen, sind [Join](joinoperator.md), [Summarize](summarizeoperator.md) und [make-series](make-seriesoperator.md).

Shuffle-Abfragestrategie kann durch den `hint.strategy = shuffle` `hint.shufflekey = <key>`Abfrageparameter oder festgelegt werden.

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

Diese Strategie teilt die Last auf allen Clusterknoten, auf denen jeder Knoten eine Partition der Daten verarbeitet.
Es ist nützlich, die Shuffle-Abfragestrategie`join` zu `summarize` verwenden, wenn der Schlüssel (Schlüssel, Schlüssel oder `make-series` Schlüssel) eine hohe Kardinalität hat, wodurch die reguläre Abfragestrategie Abfragelimits erreicht.

**Unterschied zwischen hint.strategy=shuffle und hint.shufflekey = Key**

`hint.strategy=shuffle`bedeutet, dass der gemischte Operator durch alle Tasten gemischt wird.
In dieser Abfrage:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Die Hashfunktion, die die Daten mischt, verwendet sowohl die Tasten ActivityId als auch ProcessId.

Die obige Abfrage entspricht:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Dieser Hinweis kann verwendet werden, wenn Sie daran interessiert sind, die Daten durch alle Schlüssel des gemischten Operators zu mischen, da der zusammengesetzte Schlüssel zu eindeutig ist, aber jeder Schlüssel nicht eindeutig genug ist.
Wenn der gemischte Operator andere mischbare `summarize` Operatoren wie oder `join`hat, wird die Abfrage komplexer und dann wird hint.strategy=shuffle nicht angewendet.

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

In diesem Fall, wenn `hint.strategy=shuffle` wir die anwenden (anstatt die Strategie während der Abfrageplanung`ActivityId`zu `numeric_column`ignorieren) und mischen Sie die Daten durch den zusammengesetzten Schlüssel [ , ] das Ergebnis wird nicht korrekt sein.
Die, `summarize` die auf der `join` linken Seite der Groubs `join` durch `ActivityId`eine Teilmenge der Tasten ist, die ist . Es bedeutet, `summarize` dass der `ActivityId` Wille nach dem Schlüssel gruppiert`ActivityId`wird, während die Daten durch den zusammengesetzten Schlüssel [ , `numeric_column`] partitioniert werden.
Das Mischen durch den`ActivityId`zusammengesetzten Schlüssel [ , `numeric_column`] bedeutet nicht, dass es sich um ein gültiges Shuffling für die Schlüssel-ActivityId handelt und die Ergebnisse möglicherweise falsch sind.

Dieses Beispiel vereinfacht dies unter der Annahme, dass die für einen zusammengesetzten Schlüssel verwendete Hashfunktion`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|Aktivität1|2|56|
|Aktivität1|1|65|



Wie Sie sehen, wurde der zusammengesetzte Schlüssel für beide Datensätze verschiedenen Partitionen 56 `ActivityId` und 65 `summarize` zugeordnet, aber `join` diese beiden Datensätze haben `ActivityId` den gleichen Wert, was bedeutet, dass die auf der linken Seite der, die erwartet, dass ähnliche Werte der Spalte in der gleichen Partition sein werden, entintelt falsche Ergebnisse erzeugen wird.

Löst in `hint.shufflekey` diesem Fall dieses Problem, indem der Shuffle-Schlüssel für die Verknüpfung angegeben wird, bei `hint.shufflekey = ActivityId` dem ein gemeinsamer Schlüssel für alle mischbaren Operatoren ist.
In diesem Fall ist das Shuffling sicher, beide `join` und `summarize` mischt mit dem gleichen Schlüssel, so dass alle ähnlichen Werte in der gleichen Partition defintely die Ergebnisse sind richtig:

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

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|Aktivität1|2|56|
|Aktivität1|1|65|

In der Shuffle-Abfrage ist die Standardpartitionsnummer die Clusterknotennummer. Diese Zahl kann überdiest werden, indem die Syntax `hint.num_partitions = total_partitions` verwendet wird, die die Anzahl der Partitionen steuert.

Dieser Hinweis ist nützlich, wenn der Cluster über eine kleine Anzahl von Clusterknoten verfügt, bei denen die Standardpartitionsnummer ebenfalls klein ist und die Abfrage immer noch fehlschlägt oder lange Ausführungszeit in Anspruch nimmt.

Beachten Sie, dass das Festlegen vieler Partitionen die Leistung beeinträchtigen und mehr Clusterressourcen verbrauchen kann, daher wird empfohlen, die Partitionsnummer sorgfältig auszuwählen (beginnend mit der Hint.Strategy = Shuffle und beginnen Sie, die Partitionen schrittweise zu erhöhen).

**Beispiele**

Das folgende Beispiel `summarize` zeigt, wie Shuffle die Leistung erheblich verbessert.

Die Quelltabelle verfügt über 150M-Datensätze, und die Kardinalität der Gruppe nach Schlüssel ist 10M, die auf 10 Clusterknoten verteilt ist.

Wenn Sie `summarize` die reguläre Strategie ausführen, endet die Abfrage nach 1:08, und die Speicherauslastungsspitze beträgt 3 GB:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Anzahl|
|---|
|1086|

Bei Verwendung `summarize` der Shuffle-Strategie endet die Abfrage nach 7 Sekunden, und die Speicherauslastungsspitze beträgt 0,43 GB:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Anzahl|
|---|
|1086|

Das folgende Beispiel zeigt die Verbesserung auf einem Cluster mit 2 Clusterknoten, die Tabelle hat 60M-Datensätze und die Kardinalität der Gruppe nach Schlüssel ist 2M.

Wenn Sie `hint.num_partitions` die Abfrage ohne ausführen, werden nur 2 Partitionen (als Clusterknotennummer) verwendet, und die folgende Abfrage dauert 1:10 Min.

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

Partitionen auf 10 festlegen, wird die Abfrage nach 23 Sekunden beendet: 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

Das folgende Beispiel `join` zeigt, wie Shuffle die Leistung erheblich verbessert.

Die Beispiele wurden in einem Cluster mit 10 Knoten abgetastet, in dem die Daten auf alle diese Knoten verteilt sind.

Die linke Tabelle hat 15M Datensätze, `join` wobei die Kardinalität des Schlüssels `join` 14M ist, die rechte Seite `join` des ist mit 150M Datensätzen und die Kardinalität des Schlüssels ist 10M.
Durch Ausführen der `join`regulären Strategie der endet die Abfrage nach 28 Sekunden, und die Speicherauslastungsspitze beträgt 1,43 GB:

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Bei Verwendung `join` der Shuffle-Strategie endet die Abfrage nach 4 Sekunden und die Speicherauslastungsspitze beträgt 0,3 GB:

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Versuchen Sie die gleichen Abfragen für ein `join` größeres Dataset, wobei die linke Seite des 150M und `join` die Kardinalität des Schlüssels 148M ist, die rechte Seite des ist 1.5B und die Kardinalität des Schlüssels ist 100M.

Die Abfrage mit `join` der Standardstrategie erreicht nach 4 Min. die Kusto-Grenzwerte und das Zeitlimit.
Bei Verwendung `join` der Shuffle-Strategie endet die Abfrage nach 34 Sekunden, und die Speicherauslastungsspitze beträgt 1,23 GB.


Das folgende Beispiel zeigt die Verbesserung auf einem Cluster mit 2 Clusterknoten, die Tabelle `join` hat 60M-Datensätze und die Kardinalität des Schlüssels ist 2M.
Wenn Sie `hint.num_partitions` die Abfrage ohne ausführen, werden nur 2 Partitionen (als Clusterknotennummer) verwendet, und die folgende Abfrage dauert 1:10 Min.

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

Partitionen auf 10 festlegen, wird die Abfrage nach 23 Sekunden beendet: 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```