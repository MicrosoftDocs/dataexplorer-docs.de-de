---
title: MV-Expand-Operator-Azure Daten-Explorer
description: Dieser Artikel beschreibt den MV-Expand-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: ee9c4b236344e21bbbc1da68b76710b15b519baa
ms.sourcegitcommit: 56bb7b69654900ed63310ac9537ae08b72bf7209
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85814210"
---
# <a name="mv-expand-operator"></a>mv-expand-Operator

Erweitert das Array oder den Eigenschaften Behälter mit mehreren Werten.

`mv-expand`wird auf ein [dynamisch](./scalar-data-types/dynamic.md)typisiertes Array oder eine Eigenschaften Behälter Spalte angewendet, sodass jeder Wert in der Auflistung eine separate Zeile erhält. Alle anderen Spalten in einer erweiterten Zeile werden dupliziert. 

**Syntax**

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [ `with_itemindex=` *indexcolumnname*] *ColumnName* [ `,` *ColumnName* ...] [ `limit` *ROWLIMIT*]

*T* `| mv-expand ` [ `bagexpansion=` ( `bag`  |  `array` )] [*Name* `=` ] *arrayexpression* [ `to typeof(` *Typname* `)` ] [, [*Name* `=` ] *arrayexpression* [ `to typeof(` *Typname* `)` ]...] [ `limit` *ROWLIMIT*]

**Argumente**

* *ColumnName:* Im Ergebnis werden Arrays in der benannten Spalte auf mehrere Zeilen erweitert. 
* *ArrayExpression:* Ein Ausdruck, der ein Array zurückgibt. Bei Verwendung dieses Formulars wird eine neue Spalte hinzugefügt, und die vorhandene wird beibehalten.
* *Name:* Ein Name für die neue Spalte.
* *Typname:* Gibt den zugrunde liegenden Typ der Elemente des Arrays an, der zum Typ der vom Operator erzeugten Spalte wird. Nicht konforme Werte im Array werden nicht konvertiert. Stattdessen nehmen diese Werte einen `null` Wert an.
* *RowLimit:* Die maximale Anzahl von Zeilen, die aus jeder ursprünglichen Zeile generiert werden. Der Standardwert ist 2147483647. 

  > [!Note]
  > Die Legacy-und veraltete Form des-Operators `mvexpand` haben ein Standardzeilen Limit von 128.

* *Indexcolumnname:* Wenn `with_itemindex` angegeben ist, enthält die Ausgabe eine zusätzliche Spalte (mit dem Namen *indexcolumnname*), die den Index (beginnend bei 0) des Elements in der ursprünglichen erweiterten Auflistung enthält. 

**Rückgabe**

Mehrere Zeilen für jeden der Werte in einem Array, die in der benannten Spalte oder im Array Ausdruck vorhanden sind.
Wenn mehrere Spalten oder Ausdrücke angegeben werden, werden Sie parallel erweitert. Für jede Eingabezeile gibt es so viele Ausgabezeilen, wie Elemente im längsten erweiterten Ausdruck vorhanden sind (kürzere Listen werden mit Nullen aufgefüllt). Wenn der Wert in einer Zeile ein leeres Array ist, wird die Zeile zu "Nothing" erweitert (wird im Resultset nicht angezeigt). Wenn der Wert in einer Zeile jedoch kein Array ist, wird die Zeile unverändert im Resultset gespeichert. 

Die erweiterte Spalte ist immer dynamisch typisiert. Verwenden Sie eine Umwandlung wie `todatetime()` oder `tolong()`, wenn Sie Werte berechnen oder aggregieren möchten.

Zwei Erweiterungsmodi für Eigenschaftenbehälter werden unterstützt:
* `bagexpansion=bag`: Eigenschaftenbehälter werden zu Eigenschaftenbehältern mit einem einzelnen Eintrag erweitert. Bei diesem Modus handelt es sich um die Standard Erweiterung.
* `bagexpansion=array`: Eigenschaften Behälter werden in `[` Array Strukturen mit *zwei Elementen erweitert* `,` *value* `]` , sodass ein einheitlicher Zugriff auf Schlüssel und Werte möglich ist (z. b. das Ausführen einer Aggregation mit unterschiedlicher Anzahl über Eigenschaftsnamen). 

## <a name="examples"></a>Beispiele

### <a name="single-column"></a>Einzelne Spalte

Eine einfache Erweiterung einer einzelnen Spalte:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|eine|k|
|---|---|
|1|{"Eigenschaft PROP1": "a"}|
|1|{"Prop2": "b"}|

### <a name="zipped-two-columns"></a>Zwei Spalten zippt

Wenn Sie zwei Spalten erweitern, werden die entsprechenden Spalten zuerst "zip" und dann erweitert:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|eine|k|c|
|---|---|---|
|1|{"Eigenschaft PROP1": "a"}|5|
|1|{"Prop2": "b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>Kartesisches Produkt von zwei Spalten

Wenn Sie ein kartesisches Produkt zum Erweitern von zwei Spalten erhalten möchten, erweitern Sie eins nach dem anderen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|eine|k|c|
|---|---|---|
|1|{"Eigenschaft PROP1": "a"}|5|
|1|{"Prop2": "b"}|5|

### <a name="convert-output"></a>Ausgabe konvertieren

Wenn Sie die Ausgabe eines MV-Expand-Aufschlags auf einen bestimmten Typ erzwingen möchten (Standardwert: dynamisch), verwenden Sie Folgendes `to typeof` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:string, b:dynamic, c:dynamic)["Constant", dynamic([1,2,3,4]), dynamic([6,7,8,9])]
| mv-expand b, c to typeof(int)
| getschema 
```

ColumnName|ColumnOrdinal|DateType|ColumnType
-|-|-|-
eine|0|System.String|Zeichenfolge
k|1|System.Object|dynamisch
c|2|System.Int32|INT

Beachten Sie, dass die Spalte als angezeigt wird `b` , `dynamic` während `c` als herauskommt `int` .

### <a name="using-with_itemindex"></a>Verwenden von with_itemindex

Erweiterung eines Arrays mit `with_itemindex` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1
| summarize x = make_list(x)
| mv-expand with_itemindex=Index x
```

|x|Index|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|
 
## <a name="see-also"></a>Weitere Informationen

* Weitere Beispiele finden Sie [unter Diagramm Anzahl von Live Aktivitäten im Zeit](./samples.md#chart-concurrent-sessions-over-time) Verlauf.
* [MV-Apply-](./mv-applyoperator.md) Operator.
* [fassen Sie make_list ()](makelist-aggfunction.md)zusammen, wobei es sich um die umgekehrte Funktion von MV-Expand handelt.
* [bag_unpack ()](bag-unpackplugin.md) -Plug-in zum Erweitern dynamischer JSON-Objekte in Spalten mithilfe von Eigenschaften Behälter Schlüsseln.
