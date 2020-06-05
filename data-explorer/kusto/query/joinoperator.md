---
title: 'Join-Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Join-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2961f27226175fe81b7d9c82a6366b36134d805f
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454115"
---
# <a name="join-operator"></a>join-Operator

Führt die Zeilen zweier Tabellen zusammen, um eine neue Tabelle zu erzeugen, indem Werte aus den angegebenen Spalten aus beiden Tabellen zugeordnet werden.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**Syntax**

*Linkfähig* `|` `join`[*Joinparameters*] `(` *rightfähige* `)` `on` *Attribute*

**Argumente**

* *Lefables*: die **linke** Tabelle oder der Tabellen Ausdruck (manchmal als **äußere** Tabelle bezeichnet), deren Zeilen zusammengeführt werden sollen. Wird als bezeichnet `$left` .

* *Righables*: die **Rechte** Tabelle oder der Rechte tabellarische Ausdruck (manchmal als **innere* Tabelle bezeichnet), deren Zeilen zusammengeführt werden sollen. Wird als bezeichnet `$right` .

* *Attribute*: eine oder mehrere (durch Trennzeichen getrennte) Regeln, die beschreiben, wie Zeilen von *linksfähigen* Zeilen von *rightfähigen*abgeglichen werden. Mehrere Regeln werden mithilfe des `and` logischen Operators ausgewertet.
  Eine Regel kann eine der folgenden sein:

  |Regelart        |Syntax                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Gleichheit nach Name |*ColumnName*                                    |`where`*Linksbar*. *ColumnName* `==` *Righbar*. *ColumnName*|
  |Gleichheit nach Wert|`$left.`*LeftColumn* `==` `$right.` *Rechte Spalte*|`where``$left.` *LeftColumn* `==` `$right.` *Rechte Spalte*       |

> [!NOTE]
> Im Fall von ' Equality by value ' *müssen* die Spaltennamen mit der anwendbaren Besitzer Tabelle qualifiziert werden, die durch die `$left` -und- `$right` Notationen gekennzeichnet ist.

* *Joinparameters*: NULL oder mehr (durch Leerzeichen getrennte) Parameter in der Form des *namens* `=` *Werts* , die das Verhalten der Zeilen Übereinstimmungs Operation und des Ausführungs Plans steuern. Die folgenden Parameter werden unterstützt: 

::: zone pivot="azuredataexplorer"

  |Name           |Werte                                        |BESCHREIBUNG                                  |
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
> Die standardmäßige joinkonfiguration, wenn `kind` nicht angegeben wird, ist `innerunique` . Dies unterscheidet sich von einigen anderen Analyseprodukten, die `inner` als Standardkonfiguration vorliegen. Lesen Sie die [folgenden](#join-flavors) Informationen sorgfältig durch, um die Unterschiede zwischen den verschiedenen Arten zu verstehen und sicherzustellen, dass die Abfrage die gewünschten Ergebnisse liefert.

**Rückgabe**

Das Ausgabe Schema hängt von der joinkonfiguration ab:

 * `kind=leftanti`, `kind=leftsemi`:

     Die Ergebnistabelle enthält nur Spalten von der linken Seite.

     
 * `kind=rightanti`, `kind=rightsemi`:

     Die Ergebnistabelle enthält nur Spalten von der rechten Seite.

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     Einer Spalte für jede Spalte in jeder der beiden Tabellen, einschließlich der übereinstimmenden Schlüssel. Die Spalten der rechten Seite werden bei Namenskonflikten automatisch umbenannt.

     
Ausgabedaten Sätze sind von der joinkonfiguration abhängig:

 * `kind=leftanti`, `kind=leftantisemi`

     Gibt alle Datensätze von der linken Seite zurück, für die keine Übereinstimmungen von rechts vorhanden sind.     
     
 * `kind=rightanti`, `kind=rightantisemi`

     Gibt alle Datensätze von der rechten Seite zurück, für die keine Übereinstimmungen von Links vorhanden sind.  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    Einer Zeile für jede Übereinstimmung zwischen den Eingabetabellen. Eine Übereinstimmung ist eine Zeile, die aus einer Tabelle ausgewählt wurde, die für alle `on` Felder denselben Wert wie eine Zeile in der anderen Tabelle mit diesen Einschränkungen aufweist:

   - `kind`nicht angegeben`kind=innerunique`

    Nur eine Zeile der linken Seite wird für jeden Wert des `on` -Schlüssels abgeglichen. Die Ausgabe enthält eine Zeile für jede Übereinstimmung dieser Zeile mit Zeilen der rechten Seite.
    
   - `kind=leftsemi`
   
    Gibt alle Datensätze von der linken Seite zurück, die Übereinstimmungen von rechts aufweisen.
    
   - `kind=rightsemi`
   
       Gibt alle Datensätze von der rechten Seite zurück, die Übereinstimmungen von Links aufweisen.

   - `kind=inner`
 
    Der Ausgabe enthält eine Zeile für jede Kombination von übereinstimmenden Zeilen der linken und rechten Seite.

   - `kind=leftouter` (oder `kind=rightouter` oder `kind=fullouter`)

    Zusätzlich zu den internen Übereinstimmungen ist eine Zeile für jede Zeile auf der linken (und/oder rechten) Seite vorhanden, selbst wenn es keine Übereinstimmung damit gibt. In diesem Fall enthalten die Ausgabezellen ohne Übereinstimmung NULL-Werte.
    Wenn mehrere Zeilen mit denselben Werten für diese Felder vorhanden sind, erhalten Sie Zeilen für alle Kombinationen.

 

**Tipps**

Für eine optimale Leistung:

* Wenn eine Tabelle immer kleiner als die andere ist, verwenden Sie diese als die linke (weitergeleitete) Seite der Verknüpfung.

**Beispiel**

Abrufen erweiterter Aktivitäten aus einem Protokoll, in dem einige Einträge den Start und das Ende einer Aktivität markieren 

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

[Weitere Informationen zu diesem Beispiel](./samples.md#get-sessions-from-start-and-stop-events).

## <a name="join-flavors"></a>Join-Varianten

Die genaue Variante des Join-Operators wird mit dem kind-Schlüsselwort angegeben. Ab heute unterstützt Kusto die folgenden Varianten des Join-Operators: 

|Art des Joins|BESCHREIBUNG|
|--|--|
|[`innerunique`](#default-join-flavor)(oder leer als Standard)|Innerer Join mit linker seitiger Deduplizierung|
|[`inner`](#inner-join)|Innerer Standard Beitritt|
|[`leftouter`](#left-outer-join)|Left Outer Join|
|[`rightouter`](#right-outer-join)|Rechte äußere Verknüpfung|
|[`fullouter`](#full-outer-join)|Vollständiger äußerer Join|
|[`leftanti`](#left-anti-join), [`anti`](#left-anti-join) oder[`leftantisemi`](#left-anti-join)|Linker Anti-Join|
|[`rightanti`](#right-anti-join)noch[`rightantisemi`](#right-anti-join)|Rechter Anti-Join|
|[`leftsemi`](#left-semi-join)|Linker Semijoin|
|[`rightsemi`](#right-semi-join)|Rechter Semijoin|

### <a name="inner-and-innerunique-join-operator-flavors"></a>innere und innerunique Join-Operator-Varianten

-    Bei Verwendung der **inneren**joinkonfiguration gibt es eine Zeile in der Ausgabe für jede Kombination aus übereinstimmenden Zeilen von Links und rechts ohne die deduplizierungen von linken Schlüsseln. Bei der Ausgabe handelt es sich um ein kartesisches Produkt von left-und Right-Schlüsseln.
    Beispiel für **inneren Join**:

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
| join kind = inner
    t2
on key
```

|Schlüssel|value|key1|value1|
|---|---|---|---|
|1|Val 1.2|1|Val 1.3|
|1|Val 1.1|1|Val 1.3|
|1|Val 1.2|1|Val 1.4|
|1|Val 1.1|1|Val 1.4|

-    Durch die Verwendung der **innerunique Join** -Konfiguration werden Schlüssel von Links abgeleitet, und es gibt eine Zeile in der Ausgabe jeder Kombination aus deduplizierten linken und rechten Schlüsseln.
    Beispiel für **innerunique Join** für dieselben Datasets, die oben verwendet werden. Beachten Sie, dass die **innerunique** -Konfiguration für diesen Fall möglicherweise zwei mögliche Ausgaben ergibt und beide korrekt sind.
    In der ersten Ausgabe hat der Joinoperator den ersten Schlüssel, der in T1 mit dem Wert "Val 1.1" angezeigt wird, nach dem Zufallsprinzip ausgewählt und mit T2-Schlüsseln verglichen, während er in der zweiten Ausgabe den zweiten Schlüssel zufällig ausgewählt hat, der den Wert "Val 1.2" aufweist und mit T2-Schlüsseln übereinstimmt:

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


-    Kusto ist für das Übermitteln von Filtern optimiert, die nach `join` Links oder rechts zur entsprechenden joinseite gelangen.
    Wenn die verwendete Geschmacksrichtung **innerunique** ist und der Filter auf der linken Seite des Joins weitergegeben werden kann, wird Sie in einigen Fällen automatisch weitergegeben, und die für diesen Filter verwendeten Schlüssel werden immer in der Ausgabe angezeigt.
    Wenn Sie z. b. das obige Beispiel verwenden und Filter hinzufügen, ` where value == "val1.2" ` Geben Sie immer das zweite Ergebnis an und geben nie das erste Ergebnis für die verwendeten Datasets an:

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
 
### <a name="default-join-flavor"></a>Standardmäßige joinkonfiguration
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
Verwenden Sie zwei Beispiel Tabellen, um den Vorgang des Joins zu erläutern: 
 
Tabelle X 

|Schlüssel |Wert1 
|---|---
|a |1 
|k |2 
|b |3 
|c |4 

Tabelle Y 

|Schlüssel |Wert2 
|---|---
|k |10 
|c |20 
|c |30 
|T |40 
 
Das standardmäßige Join führt einen inneren Join nach der Deduplizierung der linken Seite des Join-Schlüssels durch. (Bei der Deduplizierung wird der erste Datensatz beibehalten.) Wenn die folgende Anweisung gilt: 

    X | join Y on Key 

Würde die effektive linke Seite des Joins (Tabelle X nach Deduplizierung) folgendermaßen lauten: 

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


(Beachten Sie, dass die Schlüssel ' a ' und ' 'd ' nicht in der Ausgabe angezeigt werden, da auf der linken und der rechten Seite keine übereinstimmenden Schlüssel vorhanden waren. 
 
(In der Vergangenheit war dies die erste Implementierung der Verknüpfung, die von der ersten Version von Kusto unterstützt wird. Sie ist in typischen Szenarios für die Protokoll-und Ablauf Verfolgungs Analyse nützlich, bei denen zwei Ereignisse (die jeweils einem Filter Kriterium entsprechen) unter der gleichen Korrelations-ID korreliert werden sollen und alle Vorkommen des gesuchten Ereignisses ignoriert werden.
 
### <a name="inner-join"></a>Innere Verknüpfung

Dies ist der standardmäßige innere Join wie von SQL bekannt. Der Ausgabedatensatz wird erstellt, wenn ein Datensatz auf der linken Seite über den gleichen Join-Schlüssel verfügt wie der Datensatz auf der rechten Seite. 
 
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

Beachten Sie, dass (b,10) von der rechten Seite zweimal verknüpft wurde: mit (b,2) und (b,3) auf der linken Seite; zudem wurde (c,4) auf der linken Seite zweimal verknüpft: mit (c,20) und (c,30) auf der rechten Seite. 

### <a name="left-outer-join"></a>Left Outer Join 

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
|b|3|k|10|
|k|2|k|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

 
### <a name="right-outer-join"></a>Rechte äußere Verknüpfung 

Ähnelt dem linken äußeren Join, allerdings werden die Tabellen umgekehrt behandelt. 
 
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

 
### <a name="full-outer-join"></a>Vollständiger äußerer Join 

Im Prinzip kombiniert ein vollständiger äußerer Join den Effekt der Anwendung von sowohl linken als auch rechten äußeren Joins. Wenn die Datensätze in den verbundenen Tabellen nicht übereinstimmen, enthält das Resultset NULL-Werte für jede Spalte der Tabelle, für die keine übereinstimmende Zeile vorhanden ist. Für die Datensätze, die übereinstimmen, wird eine einzelne Zeile im Resultset erstellt (das Felder mit Werten aus beiden Tabellen enthält). 
 
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

 
### <a name="left-anti-join"></a>Linker Anti-Join

Left Anti Join gibt alle Datensätze von der linken Seite zurück, die mit keinem Datensatz von der rechten Seite identisch sind. 
 
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

Anti-Join formt die Abfrage „NOT IN“. 

### <a name="right-anti-join"></a>Rechter Anti-Join

Right Anti Join gibt alle Datensätze von der rechten Seite zurück, die mit keinem Datensatz von der linken Seite identisch sind. 
 
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

Anti-Join formt die Abfrage „NOT IN“. 

### <a name="left-semi-join"></a>Linker Semijoin

Left Semijoin gibt alle Datensätze von der linken Seite zurück, die einem Datensatz von der rechten Seite entsprechen. Es werden nur Spalten von der linken Seite zurückgegeben. 

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

### <a name="right-semi-join"></a>Rechter Semijoin

Right Semi Join gibt alle Datensätze von der rechten Seite zurück, die einem Datensatz von der linken Seite entsprechen. Es werden nur Spalten von der rechten Seite zurückgegeben. 

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


### <a name="cross-join"></a>Cross Join

Kusto stellt nicht nativ eine Kreuz Verknüpfungs Konfiguration bereit (d. h., Sie können den Operator nicht mit markieren `kind=cross` ).
Es ist jedoch nicht schwierig, dies zu simulieren, indem ein dummyschlüssel angezeigt wird:

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>Joinhinweise

Der- `join` Operator unterstützt eine Reihe von hinweisen, die Steuern, wie eine Abfrage ausgeführt wird.
Dies ändert nicht die Semantik von `join` , wirkt sich jedoch möglicherweise auf die Leistung aus.

In den folgenden Artikeln werden die Joinhinweise erläutert: 
* `hint.shufflekey=<key>`und `hint.strategy=shuffle`  -  [shuffle-Abfrage](shufflequery.md)
* `hint.strategy=broadcast` - [Broadcast-Join](broadcastjoin.md)
* `hint.remote=<strategy>` - [Cluster übergreifender Join](joincrosscluster.md)
