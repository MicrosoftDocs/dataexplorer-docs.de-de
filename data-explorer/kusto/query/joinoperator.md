---
title: Join-Operator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Join-Operator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 86d883c8d0214d278099260fa2406b91b776b4d1
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765812"
---
# <a name="join-operator"></a>join-Operator

Führt die Zeilen zweier Tabellen zusammen, um eine neue Tabelle zu erzeugen, indem Werte aus den angegebenen Spalten aus beiden Tabellen zugeordnet werden.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**Syntax**

*LeftTable* `|` `join` [*JoinParameters*] `)` `on` `(` *RightTable-Attribute* *Attributes*

**Argumente**

* *LeftTable*: Die **linke** Tabelle oder der tabellarische Ausdruck (manchmal auch **als äußere** Tabelle bezeichnet), deren Zeilen zusammengeführt werden sollen. Bezeichnet als `$left`.

* *RightTable*: Die **rechte** Tabelle oder der tabellarische Ausdruck (manchmal auch **innere* Tabelle genannt), deren Zeilen zusammengeführt werden sollen. Bezeichnet als `$right`.

* *Attribute*: Eine oder mehrere (durch Kommas getrennte) Regeln, die beschreiben, wie Zeilen aus *LeftTable* mit Zeilen aus *RightTable*abgeglichen werden. Mehrere Regeln werden `and` mit dem logischen Operator ausgewertet.
  Eine Regel kann eine der wichtigsten sein:

  |Regelart        |Syntax                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |Gleichheit nach Namen |*ColumnName*                                    |`where`*LeftTable*. *ColumnName* `==` *RightTable*. *ColumnName*|
  |Gleichheit nach Wert|`$left.`*LeftColumn* `==` `$right.` *RightColumn*|`where``$left.` *LeftColumn* `==` LeftColumn `$right.` *RightColumn*       |

> [!NOTE]
> Bei "Gleichheit nach Wert" *müssen* die Spaltennamen mit der entsprechenden `$left` Besitzertabelle, die durch Notationen `$right` bezeichnet wird, qualifiziert werden.

* *JoinParameters*: Null oder mehr (raumgetrennte) Parameter in Form von *Name* `=` *Value,* die das Verhalten des Zeilenübereinstimmungsvorgangs und des Ausführungsplans steuern. Die folgenden Parameter werden unterstützt: 

::: zone pivot="azuredataexplorer"

  |Name           |Werte                                        |BESCHREIBUNG                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |Join-Varianten|Siehe [Join Flavors](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |Siehe [Clusterübergreifende Verknüpfung](joincrosscluster.md)|
  |`hint.strategy`|Ausführungshinweise                               |Siehe [Join-Hinweise](#join-hints)                |

::: zone-end

::: zone pivot="azuremonitor"

  |Name           |Werte                                        |BESCHREIBUNG                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |Join-Varianten|Siehe [Join Flavors](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
  |`hint.strategy`|Ausführungshinweise                               |Siehe [Join-Hinweise](#join-hints)                |

::: zone-end


> [!WARNING]
> Die Standardverknüpfungsvariante, sofern `kind` nicht `innerunique`angegeben, ist . Dies unterscheidet sich von einigen `inner` anderen Analyseprodukten, die als Standardgeschmack haben. Bitte lesen Sie [unten](#join-flavors) sorgfältig, um die Unterschiede zwischen den verschiedenen Arten zu verstehen und um sicherzustellen, dass die Abfrage die beabsichtigten Ergebnisse liefert.

**Rückgabe**

Das Ausgabeschema hängt von der Verknüpfungsvariante ab:

 * `kind=leftanti`, `kind=leftsemi`:

     Die Ergebnistabelle enthält nur Spalten von der linken Seite.

     
 * `kind=rightanti`, `kind=rightsemi`:

     Die Ergebnistabelle enthält nur Spalten von der rechten Seite.

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     Einer Spalte für jede Spalte in jeder der beiden Tabellen, einschließlich der übereinstimmenden Schlüssel. Die Spalten der rechten Seite werden bei Namenskonflikten automatisch umbenannt.

     
Ausgabedatensätze hängen von der Verknüpfungsvariante ab:

 * `kind=leftanti`, `kind=leftantisemi`

     Gibt alle Datensätze von der linken Seite zurück, die keine Übereinstimmungen von rechts haben.     
     
 * `kind=rightanti`, `kind=rightantisemi`

     Gibt alle Datensätze von der rechten Seite zurück, die keine Übereinstimmungen von links haben.  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    Einer Zeile für jede Übereinstimmung zwischen den Eingabetabellen. Eine Übereinstimmung ist eine Zeile, die aus einer `on` Tabelle ausgewählt wurde, die für alle Felder den gleichen Wert wie eine Zeile in der anderen Tabelle mit diesen Einschränkungen hat:

   - `kind`Unspecified`kind=innerunique`

    Nur eine Zeile der linken Seite wird für jeden Wert des `on` -Schlüssels abgeglichen. Die Ausgabe enthält eine Zeile für jede Übereinstimmung dieser Zeile mit Zeilen der rechten Seite.
    
   - `kind=leftsemi`
   
    Gibt alle Datensätze von der linken Seite zurück, die Übereinstimmungen von rechts haben.
    
   - `kind=rightsemi`
   
       Gibt alle Datensätze von der rechten Seite zurück, die Übereinstimmungen von links haben.

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

[Weitere Informationen zu diesem Beispiel](./samples.md#activities).

## <a name="join-flavors"></a>Join-Varianten

Die genaue Variante des Join-Operators wird mit dem kind-Schlüsselwort angegeben. Ab heute unterstützt Kusto die folgenden Varianten des Join-Operators: 

|Art des Joins|BESCHREIBUNG|
|--|--|
|[`innerunique`](#default-join-flavor)(oder standardmäßig leer)|Innere Verknüpfung mit linker Deduplizierung|
|[`inner`](#inner-join)|Standard-Innenverknüpfung|
|[`leftouter`](#left-outer-join)|Left Outer Join|
|[`rightouter`](#right-outer-join)|Rechte äußere Verknüpfung|
|[`fullouter`](#full-outer-join)|Vollständige äußere Verknüpfung|
|[`leftanti`](#left-anti-join), [`anti`](#left-anti-join) oder[`leftantisemi`](#left-anti-join)|Linke Anti-Beitritt|
|[`rightanti`](#right-anti-join)Oder[`rightantisemi`](#right-anti-join)|Rechte Anti-Beitritt|
|[`leftsemi`](#left-semi-join)|Linke Halbverknüpfung|
|[`rightsemi`](#right-semi-join)|Rechte Halbverknüpfung|

### <a name="inner-and-innerunique-join-operator-flavors"></a>innere und innereeinzigartige Join-Operator-Geschmäcker

-    Bei Verwendung der **inneren Join-Geschmack,** gibt es eine Zeile in der Ausgabe für jede Kombination von übereinstimmenden Zeilen von links und rechts ohne linke Tasten Deduplizierungen. Die Ausgabe wird ein kartesisches Produkt von linken und rechten Schlüsseln sein.
    Beispiel **für innere Verknüpfung**:

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
|1|val1.2|1|val1.3|
|1|val1.1|1|val1.3|
|1|val1.2|1|val1.4|
|1|val1.1|1|val1.4|

-    Mit **innerunique Join-Geschmack** wird Schlüssel von der linken Seite dedupliziert und es wird eine Zeile in der Ausgabe von jeder Kombination von deduplizierten linken Tasten und rechten Tasten.
    Beispiel für **innerunique join** für die gleichen oben verwendeten Datasets, Bitte beachten Sie, dass **innerunique Geschmack** für diesen Fall kann zwei mögliche Ausgaben ergeben und beide sind richtig.
    In der ersten Ausgabe wählte der join-Operator zufällig die erste Taste aus, die in t1 mit dem Wert "val1.1" erscheint, und stimmte sie mit t2-Tasten ab, während in der zweiten der Join-Operator zufällig die zweite Taste in t1 ausgewählt hat, die den Wert "val1.2" hat, und sie mit t2-Tasten abgeglichen hat:

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


-    Kusto ist optimiert, um Filter `join` zu schieben, die nach der entsprechenden Join-Seite nach links oder rechts kommen, wenn möglich.
    Manchmal, wenn der verwendete Geschmack **innereinzigartig** ist und der Filter auf der linken Seite der Verknüpfung weitergegeben werden kann, wird er automatisch weitergegeben und die Schlüssel, die für diesen Filter gelten, werden immer in der Ausgabe angezeigt.
    Wenn Sie z. B. ` where value == "val1.2" ` das obige Beispiel verwenden und filter hinzufügen, wird immer das zweite Ergebnis angezeigt und nie das erste Ergebnis für die verwendeten Datasets angezeigt:

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
 
### <a name="default-join-flavor"></a>Standard-Join-Geschmack
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
Verwenden wir zwei Beispieltabellen, um die Funktionsweise der Verknüpfung zu erläutern: 
 
Tabelle X 

|Schlüssel |Wert1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Tabelle Y 

|Schlüssel |Wert2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 
 
Das standardmäßige Join führt einen inneren Join nach der Deduplizierung der linken Seite des Join-Schlüssels durch. (Bei der Deduplizierung wird der erste Datensatz beibehalten.) Wenn die folgende Anweisung gilt: 

    X | join Y on Key 

Würde die effektive linke Seite des Joins (Tabelle X nach Deduplizierung) folgendermaßen lauten: 

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


(Beachten Sie, dass die Tasten 'a' und 'd' nicht in der Ausgabe angezeigt werden, da es keine übereinstimmenden Tasten auf der linken und rechten Seite gab). 
 
(Historisch gesehen war dies die erste Implementierung der Verknüpfung, die von der ursprünglichen Version von Kusto unterstützt wurde; es ist nützlich in den typischen Log/Trace-Analyseszenarien, in denen wir zwei Ereignisse (jedes übereinstimmend mit einem Filterkriterium) unter derselben Korrelations-ID korrelieren und alle Erscheinungen des gesuchten Phänomens zurückbekommen möchten, wobei mehrere Erscheinungen der beitragenden Ablaufverfolgungsdatensätze ignoriert werden.)
 
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
|b|3|b|10|
|b|2|b|10|
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
|b|3|b|10|
|b|2|b|10|
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
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

 
### <a name="full-outer-join"></a>Vollständige äußere Verknüpfung 

Im Prinzip kombiniert ein vollständiger äußerer Join den Effekt der Anwendung von sowohl linken als auch rechten äußeren Joins. Wenn Datensätze in den verknüpften Tabellen nicht übereinstimmen, enthält das Resultset NULL-Werte für jede Spalte der Tabelle, in der keine übereinstimmende Zeile vorhanden ist. Für die Datensätze, die übereinstimmen, wird eine einzelne Zeile im Resultset erstellt (das Felder mit Werten aus beiden Tabellen enthält). 
 
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
|||d|40|
|a|1|||

 
### <a name="left-anti-join"></a>Linke Anti-Beitritt

Linke Anti-Join gibt alle Datensätze von der linken Seite zurück, die keinem Datensatz von der rechten Seite entsprechen. 
 
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

### <a name="right-anti-join"></a>Rechte Anti-Beitritt

Die rechte Anti-Join-Verknüpfung gibt alle Datensätze von der rechten Seite zurück, die keinem Datensatz von der linken Seite entsprechen. 
 
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
|d|40|

Anti-Join formt die Abfrage „NOT IN“. 

### <a name="left-semi-join"></a>Linke Halbverknüpfung

Die linke Halbverknüpfung gibt alle Datensätze von der linken Seite zurück, die mit einem Datensatz von der rechten Seite übereinstimmen. Nur Spalten von der linken Seite werden zurückgegeben. 

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

### <a name="right-semi-join"></a>Rechte Halbverknüpfung

Die rechte Halbverknüpfung gibt alle Datensätze von der rechten Seite zurück, die mit einem Datensatz von der linken Seite übereinstimmen. Nur Spalten von der rechten Seite werden zurückgegeben. 

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

Kusto bietet keinen verknüpfungsübergreifenden Geschmack (d. h., Sie können den `kind=cross`Operator nicht mit markieren).
Es ist jedoch nicht schwer, dies zu simulieren, indem Sie einen Dummy-Schlüssel aufstellen:

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>Join-Tipps

Der `join` Operator unterstützt eine Reihe von Hinweisen, die die Ausführung einer Abfrage steuern.
Diese ändern nicht die Semantik von `join`, können aber ihre Leistung beeinträchtigen.

Join-Hinweise, die in den folgenden Artikeln erläutert werden: 
* `hint.shufflekey=<key>`und `hint.strategy=shuffle`  -  [Shuffle-Abfrage](shufflequery.md)
* `hint.strategy=broadcast` - [Broadcast-Beitritt](broadcastjoin.md)
* `hint.remote=<strategy>` - [Clusterübergreifende Verknüpfung](joincrosscluster.md)
