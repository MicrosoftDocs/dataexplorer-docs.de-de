---
title: 'Join-Operator: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt den Join-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8324d0c6537d6d22a2814a7aa80625278dc36aec
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241513"
---
# <a name="join-operator"></a>join-Operator

Führen Sie die Zeilen zweier Tabellen aus, um eine neue Tabelle zu bilden, indem Sie die Werte der angegebenen Spalten aus den einzelnen Tabellen abgleichen.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>Syntax

*Linkfähig* `|` `join` [*Joinparameters*] `(` *rightfähige* `)` `on` *Attribute*

## <a name="arguments"></a>Argumente

* *Lefables*: die **linke** Tabelle oder der Tabellen Ausdruck, die manchmal als **äußere** Tabelle bezeichnet werden, deren Zeilen zusammengeführt werden sollen. Wird als bezeichnet `$left` .

* *Righables*: die **Rechte** Tabelle oder der Rechte tabellarische Ausdruck, die manchmal als **innere** Tabelle bezeichnet werden, deren Zeilen zusammengeführt werden sollen. Wird als bezeichnet `$right` .

* *Attribute*: eine oder mehrere durch Trennzeichen getrennte **Regeln** , die beschreiben, wie Zeilen von *linksfähigen* Zeilen von *rightfähigen*abgeglichen werden. Mehrere Regeln werden mithilfe des `and` logischen Operators ausgewertet.

  Eine **Regel** kann eine der folgenden sein:

  |Regelart        |Syntax          |Predicate    |
  |-----------------|--------------|-------------------------|
  |Gleichheit nach Name |*ColumnName*    |`where`*Linksbar*. *ColumnName* `==` *Righbar*. *ColumnName*|
  |Gleichheit nach Wert|`$left.`*LeftColumn* `==` `$right.` *Rechte Spalte*|`where``$left.` *LeftColumn* `==` `$right.` *Rechte Spalte*       |

    > [!NOTE]
    > Für ' Gleichheit nach Wert ' *müssen* die Spaltennamen mit der anwendbaren Besitzer Tabelle qualifiziert werden, die durch die `$left` -und- `$right` Notationen gekennzeichnet ist.

* *Joinparameters*: NULL oder mehr durch Leerzeichen getrennte Parameter in Form eines *namens* `=` *Werts* , die das Verhalten des Zeilen Übereinstimmungs Vorgangs und Ausführungs Plans steuern. Die folgenden Parameter werden unterstützt:

    ::: zone pivot="azuredataexplorer"

    |Parameter Name           |Werte                                        |BESCHREIBUNG                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Join-Varianten|Siehe [joinvarianten](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |Siehe [Cluster übergreifende](joincrosscluster.md) Verknüpfung|
    |`hint.strategy`|Ausführungs Hinweise                               |Siehe [Joinhinweise](#join-hints)                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |Name           |Werte                                        |BESCHREIBUNG                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Join-Varianten|Siehe [joinvarianten](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|Ausführungs Hinweise                               |Siehe [Joinhinweise](#join-hints)                |

    ::: zone-end

> [!WARNING]
> Wenn `kind` nicht angegeben wird, ist die standardmäßige joinkonfiguration `innerunique` . Dies unterscheidet sich von anderen Analyseprodukten, die über `inner` die Standardkonfiguration verfügen.  Informationen zu den Unterschieden finden Sie unter [Join-Varianten](#join-flavors) , um sicherzustellen, dass die Abfrage die gewünschten Ergebnisse liefert.

## <a name="returns"></a>Rückgabe

**Das Ausgabe Schema hängt von der joinkonfiguration ab:**

| Joinkonfiguration | Ausgabeschema |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| Die Ergebnistabelle enthält nur Spalten von der linken Seite.|
| `kind=rightanti`, `kind=rightsemi` | Die Ergebnistabelle enthält nur Spalten von der rechten Seite.|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  Einer Spalte für jede Spalte in jeder der beiden Tabellen, einschließlich der übereinstimmenden Schlüssel. Die Spalten der rechten Seite werden bei Namenskonflikten automatisch umbenannt. |
   
**Ausgabedaten Sätze sind von der joinkonfiguration abhängig:**

   > [!NOTE]
   > Wenn mehrere Zeilen mit denselben Werten für diese Felder vorhanden sind, erhalten Sie Zeilen für alle Kombinationen.
   > Eine Übereinstimmung ist eine ausgewählte Zeile in einer Tabelle, die in allen `on` -Feldern denselben Wert wie eine Zeile in der anderen Tabelle aufweist.

| Joinkonfiguration | Ausgabedaten Sätze |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| Gibt alle Datensätze von der linken Seite zurück, für die keine Übereinstimmungen von rechts vorhanden sind.|
| `kind=rightanti`, `kind=rightantisemi`| Gibt alle Datensätze von der rechten Seite zurück, für die keine Übereinstimmungen von Links vorhanden sind.|
| `kind` nicht angegeben `kind=innerunique`| Nur eine Zeile der linken Seite wird für jeden Wert des `on` -Schlüssels abgeglichen. Die Ausgabe enthält eine Zeile für jede Übereinstimmung dieser Zeile mit Zeilen der rechten Seite.|
| `kind=leftsemi`| Gibt alle Datensätze von der linken Seite zurück, die Übereinstimmungen von rechts aufweisen. |
| `kind=rightsemi`| Gibt alle Datensätze von der rechten Seite zurück, die Übereinstimmungen von Links aufweisen. |
|`kind=inner`| Enthält eine Zeile in der Ausgabe für jede Kombination aus übereinstimmenden Zeilen von links nach rechts. |
| `kind=leftouter` (oder `kind=rightouter` oder `kind=fullouter`)| Enthält eine Zeile für jede Zeile auf der linken und rechten Seite, auch wenn keine Entsprechung vorliegt. Die nicht übereinstimmenden Ausgabe Zellen enthalten Nullen. |

> [!TIP]
> Wenn eine Tabelle immer kleiner als die andere Tabelle ist, verwenden Sie diese als linke (weitergeleitete) Seite des Joins, um eine optimale Leistung zu erzielen.

## <a name="example"></a>Beispiel

Erhalten Sie erweiterte Aktivitäten aus einer `login` , die einige Einträge als Anfang und Ende einer Aktivität markieren.

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityId, StartTime=timestamp
| join (Events
    | where Name == "Stop"
        | project StopTime=timestamp, ActivityId)
    on ActivityId
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityIdLeft = ActivityId, StartTime=timestamp
| join (Events
        | where Name == "Stop"
        | project StopTime=timestamp, ActivityIdRight = ActivityId)
    on $left.ActivityIdLeft == $right.ActivityIdRight
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

## <a name="join-flavors"></a>Join-Varianten

Der genaue Typ des Join-Operators wird mit dem *Kind* -Schlüsselwort angegeben. Die folgenden Varianten des Join-Operators werden unterstützt:

|Joinart/-Konfiguration|BESCHREIBUNG|
|--|--|
|[`innerunique`](#default-join-flavor) (oder leer als Standard)|Innerer Join mit linker seitiger Deduplizierung|
|[`inner`](#inner-join-flavor)|Innerer Standard Beitritt|
|[`leftouter`](#left-outer-join-flavor)|Left Outer Join|
|[`rightouter`](#right-outer-join-flavor)|Rechte äußere Verknüpfung|
|[`fullouter`](#full-outer-join-flavor)|Vollständiger äußerer Join|
|[`leftanti`](#left-anti-join-flavor), [`anti`](#left-anti-join-flavor) oder [`leftantisemi`](#left-anti-join-flavor)|Linker Anti-Join|
|[`rightanti`](#right-anti-join-flavor) oder [`rightantisemi`](#right-anti-join-flavor)|Rechter Anti-Join|
|[`leftsemi`](#left-semi-join-flavor)|Linker Semijoin|
|[`rightsemi`](#right-semi-join-flavor)|Rechter Semijoin|

### <a name="default-join-flavor"></a>Standardmäßige joinkonfiguration

Die standardmäßige joinkonfiguration ist eine innere Verknüpfung mit der linken Seite der Deduplizierung. Die standardjoinimplementierung ist in typischen Szenarios für die Protokoll-und Ablauf Verfolgungs Analyse hilfreich, in denen zwei Ereignisse korreliert werden sollen, die jeweils mit einem Filter Kriterium übereinstimmen. Sie möchten alle Vorkommen des Phänomens wiederholen und mehrere Vorkommen der Mitwirkenden Ablauf Verfolgungs Datensätze ignorieren.

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

Die folgenden beiden Beispiel Tabellen werden verwendet, um den Vorgang des Joins zu erläutern.

**Tabelle X**

|Schlüssel |Wert1
|---|---
|a |1
|k |2
|b |3
|c |4

**Tabelle Y**

|Schlüssel |Wert2
|---|---
|k |10
|c |20
|c |30
|T |40

Der Standard Join führt einen inneren Join aus, nachdem die Deduplizierung der linken Seite auf dem joinschlüssel durchführt (bei der Deduplizierung wird der erste Datensatz beibehalten).

Bei dieser Anweisung: `X | join Y on Key`

die effektive linke Seite des Joins, Tabelle X nach der Deduplizierung, wäre:

|Schlüssel |Wert1
|---|---
|a |1
|k |2
|c |4

Das Ergebnis des Joins wäre:

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join Y on Key
```

|Schlüssel|Wert1|Key1|Wert2|
|---|---|---|---|
|k|2|k|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> Die Schlüssel ' a ' und ' 'd ' werden nicht in der Ausgabe angezeigt, da auf der linken und der rechten Seite keine übereinstimmenden Schlüssel vorhanden waren.

### <a name="inner-join-flavor"></a>Innere joinkonfiguration

Die innere joinfunktion ähnelt der standardmäßigen inneren Verknüpfung aus der SQL-Welt. Ein Ausgabedatensatz wird erstellt, wenn ein Datensatz auf der linken Seite denselben joinschlüssel wie der Datensatz auf der rechten Seite aufweist.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=inner Y on Key
```

|Schlüssel|Wert1|Key1|Wert2|
|---|---|---|---|
|b|3|k|10|
|k|2|k|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> * (b, 10) auf der rechten Seite wurde zweimal verknüpft: mit (b, 2) und (b, 3) auf der linken Seite.
> * (c, 4) auf der linken Seite wurde zweimal verknüpft: mit (c, 20) und (c, 30) auf der rechten Seite.

### <a name="innerunique-join-flavor"></a>Innerunique-Join-Konfiguration
 
Verwenden Sie die **innerunique-Join-** Konfiguration, um Schlüssel von der linken Seite zu deduplizieren. Das Ergebnis ist eine Zeile in der Ausgabe aller Kombinationen aus deduplizierten linken und rechten Schlüsseln.

> [!NOTE]
> die **innerunique** -Konfiguration kann zwei mögliche Ausgaben ergeben, und beide sind richtig.
In der ersten Ausgabe hat der Joinoperator den ersten Schlüssel, der in T1 mit dem Wert "Val 1.1" angezeigt wird, nach dem Zufallsprinzip ausgewählt und mit T2-Schlüsseln abgeglichen.
In der zweiten Ausgabe hat der Join-Operator zufällig den zweiten Schlüssel ausgewählt, der in T1 mit dem Wert "Val 1.2" angezeigt wird, und mit T2-Schlüsseln übereinstimmen.

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3",
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|Schlüssel|value|key1|value1|
|---|---|---|---|
|1|Val 1.1|1|Val 1.3|
|1|Val 1.1|1|Val 1.4|

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|Schlüssel|value|key1|value1|
|---|---|---|---|
|1|Val 1.2|1|Val 1.3|
|1|Val 1.2|1|Val 1.4|

* Kusto ist für das Übermitteln von Filtern, die nach dem zur `join` entsprechenden joinseite stehen, nach links oder rechts optimiert.

* Manchmal ist der verwendete Wert **innerunique** , und der Filter wird auf der linken Seite des Joins weitergegeben. Der Typ wird automatisch weitergegeben, und die Schlüssel, die für diesen Filter gelten, werden immer in der Ausgabe angezeigt.
    
* Verwenden Sie das obige Beispiel, und fügen Sie einen Filter hinzu `where value == "val1.2" ` . Dabei wird immer das zweite Ergebnis angezeigt, und es wird nie das erste Ergebnis für die Datasets zurückgibt:

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
| where value == "val1.2"
```

|Schlüssel|value|key1|value1|
|---|---|---|---|
|1|Val 1.2|1|Val 1.3|
|1|Val 1.2|1|Val 1.4|

### <a name="left-outer-join-flavor"></a>Linker äußerer Jointyp

Das Ergebnis einer linken äußeren Verknüpfung für die Tabellen X und Y enthält immer alle Datensätze der linken Tabelle (x), auch wenn die Joinbedingung keinen übereinstimmenden Datensatz in der rechten Tabelle (Y) findet.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftouter Y on Key
```

|Schlüssel|Wert1|Key1|Wert2|
|---|---|---|---|
|b|3|k|10|
|k|2|k|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

### <a name="right-outer-join-flavor"></a>Rechter äußerer Join-Typ

Die Rechte äußere joinkonfiguration ähnelt der linken äußeren Verknüpfung, die Verarbeitung der Tabellen wird jedoch umgekehrt.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightouter Y on Key
```

|Schlüssel|Wert1|Key1|Wert2|
|---|---|---|---|
|b|3|k|10|
|k|2|k|10|
|c|4|c|20|
|c|4|c|30|
|||T|40|

### <a name="full-outer-join-flavor"></a>Vollständiger äußerer Join-Vorgang

Ein vollständiger äußerer Join kombiniert die Auswirkung der Anwendung von linken und rechten äußeren Joins. Wenn Datensätze in den verbundenen Tabellen nicht übereinstimmen, enthält das Resultset `null` Werte für jede Spalte der Tabelle, für die keine übereinstimmende Zeile vorhanden ist. Für Datensätze, die eine Entsprechung aufweisen, wird eine einzelne Zeile im Resultset erstellt, die aus beiden Tabellen aufgefüllte Felder enthält.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=fullouter Y on Key
```

|Schlüssel|Wert1|Key1|Wert2|
|---|---|---|---|
|b|3|k|10|
|k|2|k|10|
|c|4|c|20|
|c|4|c|30|
|||T|40|
|a|1|||

### <a name="left-anti-join-flavor"></a>Linke Anti-Join-Konfiguration

Linker Anti-Join gibt alle Datensätze von der linken Seite zurück, die mit keinem Datensatz von der rechten Seite identisch sind.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftanti Y on Key
```

|Schlüssel|Wert1|
|---|---|
|a|1|

> [!NOTE]
> Anti-Join formt die Abfrage „NOT IN“.

### <a name="right-anti-join-flavor"></a>Rechter Anti-Join-Typ

Der Rechte Anti-Join gibt alle Datensätze von der rechten Seite zurück, die mit keinem Datensatz von der linken Seite identisch sind.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightanti Y on Key
```

|Schlüssel|Wert2|
|---|---|
|T|40|

> [!NOTE]
> Anti-Join formt die Abfrage „NOT IN“.

### <a name="left-semi-join-flavor"></a>Linker halbjointyp

Linker Semijoin gibt alle Datensätze von der linken Seite zurück, die einem Datensatz von der rechten Seite entsprechen. Es werden nur Spalten von der linken Seite zurückgegeben.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftsemi Y on Key
```

|Schlüssel|Wert1|
|---|---|
|b|3|
|k|2|
|c|4|

### <a name="right-semi-join-flavor"></a>Rechter halbjointyp

Rechter Semijoin gibt alle Datensätze von der rechten Seite zurück, die einem Datensatz von der linken Seite entsprechen. Es werden nur Spalten von der rechten Seite zurückgegeben.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightsemi Y on Key
```

|Schlüssel|Wert2|
|---|---|
|k|10|
|c|20|
|c|30|

### <a name="cross-join"></a>Cross-Join

Kusto stellt nicht nativ eine Kreuz Verknüpfungs Konfiguration bereit. Der Operator kann nicht mit markiert werden `kind=cross` .
Verwenden Sie zum simulieren eine Pseudo Taste.

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>Joinhinweise

Der- `join` Operator unterstützt eine Reihe von hinweisen, die Steuern, wie eine Abfrage ausgeführt wird.
Diese Hinweise ändern nicht die Semantik von `join` , können sich jedoch auf die Leistung auswirken.

Joinhinweise werden in den folgenden Artikeln erläutert:

* `hint.shufflekey=<key>`und `hint.strategy=shuffle`  -  [shuffle-Abfrage](shufflequery.md)
* `hint.strategy=broadcast` - [Broadcast-Join](broadcastjoin.md)
* `hint.remote=<strategy>` - [Cluster übergreifender Join](joincrosscluster.md)
