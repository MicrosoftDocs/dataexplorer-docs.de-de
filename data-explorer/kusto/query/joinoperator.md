---
title: 'join-Operator: Azure Data Explorer'
description: In diesem Artikel wird der join-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b90e5f1c95ec75a946490cd75b5dd89ad2cb1aba
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513334"
---
# <a name="join-operator"></a>join-Operator

Mergt die Zeilen zweier Tabellen, um eine neue Tabelle zu erzeugen, indem Werte aus den angegebenen Spalten aus beiden Tabellen zugeordnet werden.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>Syntax

*LeftTable* `|` `join` [*JoinParameters*] `(` *RightTable* `)` `on` *Attributes*

## <a name="arguments"></a>Argumente

* *LeftTable*: Die **linke** Tabelle oder der tabellarischen Ausdruck, die bzw. der manchmal als **äußere** Tabelle bezeichnet wird, deren Zeilen gemergt werden sollen. Wird als `$left` bezeichnet.

* *RightTable*: Die **rechte** Tabelle oder der tabellarischen Ausdruck, die bzw. der manchmal als **innere** Tabelle bezeichnet wird, deren Zeilen gemergt werden sollen. Wird als `$right` bezeichnet.

* *Attribute*: Eine oder mehrere durch Trennzeichen getrennte **Regeln**, die beschreiben, wie Zeilen aus *LeftTable* mit Zeilen aus *RightTable* abgeglichen werden. Mehrere Regeln werden mithilfe des logischen Operators `and` ausgewertet.

  Folgende **Regeln** sind möglich:

  |Regelart        |Syntax          |Predicate    |
  |-----------------|--------------|-------------------------|
  |Gleichheit nach Name |*ColumnName*    |`where` *LeftTable*.*ColumnName* `==` *RightTable*.*ColumnName*|
  |Gleichheit nach Wert|`$left.`*LeftColumn* `==` `$right.`*RightColumn*|`where` `$left.`*LeftColumn* `==` `$right.`*RightColumn*       |

    > [!NOTE]
    > Für „Gleichheit nach Wert“ *müssen* die Spaltennamen mit der anwendbaren Besitzertabelle qualifiziert werden, die durch die Notationen `$left` und `$right` gekennzeichnet ist.

* *JoinParameters*: Null oder mehr durch Leerzeichen getrennte Parameter in Form von *Name* `=` *Wert*, die das Verhalten des Vorgangs zum Zuordnen der Zeilen und den Ausführungsplan steuern. Die folgenden Parameter werden unterstützt:

    ::: zone pivot="azuredataexplorer"

    |Parametername           |Werte                                        |BESCHREIBUNG                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Join-Varianten|Siehe [Join-Varianten](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |Siehe [clusterübergreifender Join](joincrosscluster.md)|
    |`hint.strategy`|Ausführungshinweise                               |Siehe [Join-Hinweise](#join-hints)                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |Name           |Werte                                        |BESCHREIBUNG                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Join-Varianten|Siehe [Join-Varianten](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|Ausführungshinweise                               |Siehe [Join-Hinweise](#join-hints)                |

    ::: zone-end

> [!WARNING]
> Wenn `kind` nicht angegeben ist, ist die Join-Standardvariante `innerunique`. Dies unterscheidet sich von anderen Analyseprodukten, die `inner` als Standardvariante verwenden.  Informationen zu den Unterschieden finden Sie unter [Join-Varianten](#join-flavors), damit Sie sicherstellen können, dass die Abfrage die gewünschten Ergebnisse liefert.

## <a name="returns"></a>Gibt zurück

**Das Ausgabeschema hängt von der Join-Variante ab:**

| Join-Variante | Ausgabeschema |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| Die Ergebnistabelle enthält nur Spalten von der linken Seite.|
| `kind=rightanti`, `kind=rightsemi` | Die Ergebnistabelle enthält nur Spalten von der rechten Seite.|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  Eine Spalte für jede Spalte in jeder der beiden Tabellen, einschließlich der übereinstimmenden Schlüssel. Die Spalten der rechten Seite werden bei Namenskonflikten automatisch umbenannt. |
   
**Ausgabedatensätze sind von der Join-Variante abhängig:**

   > [!NOTE]
   > Wenn mehrere Zeilen mit denselben Werten für diese Felder vorhanden sind, erhalten Sie Zeilen für alle Kombinationen.
   > Eine Übereinstimmung ist eine ausgewählte Zeile in einer Tabelle, die in allen `on` -Feldern denselben Wert wie eine Zeile in der anderen Tabelle aufweist.

| Join-Variante | Ausgabedatensätze |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| Gibt alle Datensätze von der linken Seite zurück, für die keine Übereinstimmungen auf der rechten Seite vorhanden sind.|
| `kind=rightanti`, `kind=rightantisemi`| Gibt alle Datensätze von der rechten Seite zurück, für die keine Übereinstimmungen auf der linken Seite vorhanden sind.|
| `kind` nicht angegeben, `kind=innerunique`| Nur eine Zeile der linken Seite wird für jeden Wert des `on` -Schlüssels abgeglichen. Die Ausgabe enthält eine Zeile für jede Übereinstimmung dieser Zeile mit Zeilen der rechten Seite.|
| `kind=leftsemi`| Gibt alle Datensätze von der linken Seite zurück, für die Übereinstimmungen auf der rechten Seite vorhanden sind. |
| `kind=rightsemi`| Gibt alle Datensätze von der rechten Seite zurück, für die Übereinstimmungen auf der linken Seite vorhanden sind. |
|`kind=inner`| Enthält eine Zeile in der Ausgabe für jede Kombination von übereinstimmenden Zeilen von der linken und rechten Seite. |
| `kind=leftouter` (oder `kind=rightouter` oder `kind=fullouter`)| Enthält eine Zeile für jede Zeile auf der linken und rechten Seite, auch wenn keine Übereinstimmung vorliegt. Die Ausgabezellen ohne Übereinstimmung enthalten NULL-Werte. |

> [!TIP]
> Wenn eine Tabelle immer kleiner als die andere ist, verwenden Sie diese für die bestmögliche Leistung als die linke (weitergeleitete) Seite der Verknüpfung.

## <a name="example"></a>Beispiel

Abrufen erweiterter Aktivitäten aus einem `login`, in dem einige Einträge den Start und das Ende einer Aktivität markieren.

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

Die genaue Variante des Join-Operators wird mit dem *kind*-Schlüsselwort angegeben. Die folgenden Varianten des Join-Operators werden unterstützt:

|Join-Art/-Variante|BESCHREIBUNG|
|--|--|
|[`innerunique`](#default-join-flavor) (oder leer als Standardwert)|Innerer Join mit linker seitiger Deduplizierung|
|[`inner`](#inner-join-flavor)|Innerer Standard-Join|
|[`leftouter`](#left-outer-join-flavor)|Left Outer Join|
|[`rightouter`](#right-outer-join-flavor)|Rechte äußere Verknüpfung|
|[`fullouter`](#full-outer-join-flavor)|Vollständiger äußerer Join|
|[`leftanti`](#left-anti-join-flavor), [`anti`](#left-anti-join-flavor) oder [`leftantisemi`](#left-anti-join-flavor)|Linker Anti-Join|
|[`rightanti`](#right-anti-join-flavor) oder [`rightantisemi`](#right-anti-join-flavor)|Rechter Anti-Join|
|[`leftsemi`](#left-semi-join-flavor)|Linker Semi-Join|
|[`rightsemi`](#right-semi-join-flavor)|Rechter Semi-Join|

### <a name="default-join-flavor"></a>Join-Standardvariante

Die Join- Standardvariante ist ein innerer Join mit linksseitiger Deduplizierung. Die Join-Standardimplementierung ist in typischen Protokoll- und Ablaufverfolgungsanalyse-Szenarien hilfreich, in denen zwei Ereignisse korreliert werden sollen, die jeweils mit einem Filterkriterium übereinstimmen. Sie möchten alle Vorkommen des Phänomens zurückgeben und mehrere Vorkommen der beitragenden Stapelüberwachungsdatensätze ignorieren.

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

Die folgenden beiden Beispieltabellen werden verwendet, um den Vorgang des Joins zu erläutern.

**Tabelle X**

|Schlüssel |Wert1
|---|---
|a |1
|b |2
|b |3
|c |4

**Tabelle Y**

|Schlüssel |Wert2
|---|---
|b |10
|c |20
|c |30
|T |40

Der standardmäßige Join führt einen inneren Join nach der Deduplizierung der linken Seite für den Join-Schlüssel durch (die Deduplizierung behält den ersten Datensatz bei).

Wenn die folgende Anweisung gilt: `X | join Y on Key`,

würde die effektive linke Seite des Joins (Tabelle X nach Deduplizierung) folgendermaßen lauten:

|Schlüssel |Wert1
|---|---
|a |1
|b |2
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
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> Die Schlüssel „a“ und „d“ werden nicht in der Ausgabe angezeigt, da es keine übereinstimmenden Schlüssel links und rechts gab.

### <a name="inner-join-flavor"></a>Inner-Join-Variante

Die Inner-Join-Funktion ähnelt dem Standard-inner-join aus der SQL-Welt. Ein Ausgabedatensatz wird erstellt, wenn ein Datensatz auf der linken Seite über den gleichen Join-Schlüssel verfügt wie der Datensatz auf der rechten Seite.

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
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> * Für (b,10) auf der rechten Seite wurden zwei Joins verwendet: mit (b,2) und (b,3) auf der linken Seite.
> * Für (c,4) auf der linken Seite wurden zwei Joins verwendet: mit (c,20) und (c,30) auf der rechten Seite.

### <a name="innerunique-join-flavor"></a>Innerunique-Join-Variante
 
Verwenden Sie die **Innerunique-Join-Variante**, um Schlüssel von der linken Seite zu deduplizieren. Das Ergebnis ist eine Zeile in der Ausgabe aus jeder Kombination aus deduplizierten linken und rechten Schlüsseln.

> [!NOTE]
> Die **innerunique-Variante** kann zwei mögliche Ausgaben ergeben, und beide sind richtig.
In der ersten Ausgabe hat der join-Operator den ersten Schlüssel, der in t1 mit dem Wert „val1.1“ vorhanden ist, nach dem Zufallsprinzip ausgewählt und mit t2-Schlüsseln abgeglichen.
In der zweiten Ausgabe hat der join-Operator den zweiten Schlüssel, der in t1 mit dem Wert „val1.2“ vorhanden ist, nach dem Zufallsprinzip ausgewählt und mit t2-Schlüsseln abgeglichen.

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
|1|val1.1|1|val1.3|
|1|val1.1|1|val1.4|

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
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

* Kusto ist so optimiert, dass Filter, die nach dem `join` vorhanden sind, nach Möglichkeit auf die entsprechende Join-Seite (linke oder rechte Seite) gepusht werden.

* Manchmal wird **innerunique** als Variante verwendet, und der Filter wird an die linke Seite des Joins weitergegeben. Die Variante wird automatisch weitergegeben, und die Schlüssel, die für diesen Filter gelten, werden immer in der Ausgabe angezeigt.
    
* Verwenden Sie das Beispiel oben, und fügen Sie einen Filter `where value == "val1.2" ` hinzu. Dabei wird immer das zweite Ergebnis und nie das erste Ergebnis für die Datasets zurückgegeben:

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
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

### <a name="left-outer-join-flavor"></a>Linke Outer-Join-Variante

Das Ergebnis eines linken äußeren Joins für die Tabellen X und Y enthält immer alle Datensätze der linken Tabelle (X), auch wenn die Join-Bedingung keinen übereinstimmenden Datensatz in der rechten Tabelle (Y) findet.

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
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

### <a name="right-outer-join-flavor"></a>Rechte Outer-Join-Variante

Die rechte Outer-Join-Variante ähnelt dem linken Outer-Join, allerdings werden die Tabellen umgekehrt behandelt.

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
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||T|40|

### <a name="full-outer-join-flavor"></a>Vollständige Outer-Join-Variante

Ein vollständiger Outer-Join kombiniert den Effekt der Anwendung von sowohl linken als auch rechten Outer-Joins. Wenn Datensätze in verknüpften Tabellen nicht übereinstimmen, hat das Resultset für jede Spalte der Tabelle, für die eine übereinstimmende Zeile fehlt, `null`-Werte. Für die Datensätze, die übereinstimmen, wird eine einzelne Zeile im Resultset erstellt, das Felder mit Werten aus beiden Tabellen enthält.

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
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||T|40|
|a|1|||

### <a name="left-anti-join-flavor"></a>Linke Anti-Join-Variante

Bei einem linken Anti-Join werden alle Datensätzen von der linken Seite zurückgegeben, die nicht mit einem Datensatz von der rechten Seite übereinstimmen.

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

### <a name="right-anti-join-flavor"></a>Rechte Anti-Join-Variante

Bei einem rechten Anti-Join werden alle Datensätzen von der rechten Seite zurückgegeben, die nicht mit einem Datensatz von der linken Seite übereinstimmen.

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

### <a name="left-semi-join-flavor"></a>Linke Semi-Join-Variante

Bei einem linken Semi-Join werden alle Datensätzen von der linken Seite zurückgegeben, die mit einem Datensatz von der rechten Seite übereinstimmen. Es werden nur Spalten von der linken Seite zurückgegeben.

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
|b|2|
|c|4|

### <a name="right-semi-join-flavor"></a>Rechte Semi-Join-Variante

Bei einem rechten Semi-Join werden alle Datensätzen von der rechten Seite zurückgegeben, die mit einem Datensatz von der linken Seite übereinstimmen. Es werden nur Spalten von der rechten Seite zurückgegeben.

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
|b|10|
|c|20|
|c|30|

### <a name="cross-join"></a>Cross-Join

Kusto stellt nicht nativ eine Cross-Join-Variante bereit. Der Operator kann nicht mit `kind=cross` markiert werden.
Verwenden Sie zum Simulieren einen Pseudoschlüssel.

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>Join-Hinweise

Der `join`-Operator unterstützt eine Reihe von Hinweisen, die steuern, wie eine Abfrage ausgeführt wird.
Diese Hinweise ändern nicht die Semantik von `join`, können sich jedoch auf die Leistung auswirken.

Join-Hinweise werden in den folgenden Artikeln erläutert:

* `hint.shufflekey=<key>` und `hint.strategy=shuffle` - [Shuffleabfrage](shufflequery.md)
* `hint.strategy=broadcast` - [Broadcast-Join](broadcastjoin.md)
* `hint.remote=<strategy>` - [Clusterübergreifender Join](joincrosscluster.md)
