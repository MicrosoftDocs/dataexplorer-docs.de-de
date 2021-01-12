---
title: 'mv-expand-Operator: Azure Data Explorer'
description: In diesem Artikel wird der mv-expand-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.localizationpriority: high
ms.openlocfilehash: e439fff119e005e44a0649fe22cadf3614ce036d
ms.sourcegitcommit: 555f3da35fe250fabd35fcc6014bf055ef8405db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972497"
---
# <a name="mv-expand-operator"></a>mv-expand-Operator

Erweitert das Array oder den Eigenschaftenbehälter mit mehreren Werten.

`mv-expand` wird auf ein Array oder einen Eigenschaftenbehälter des Typs [dynamisch](./scalar-data-types/dynamic.md) angewendet, sodass jeder Wert in der Sammlung eine gesonderte Zeile erhält. Alle anderen Spalten in einer erweiterten Zeile werden dupliziert. 

## <a name="syntax"></a>Syntax

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>Argumente

* *ColumnName:* Im Ergebnis werden Arrays in der benannten Spalte auf mehrere Zeilen erweitert. 
* *ArrayExpression:* Ein Ausdruck, der ein Array zurückgibt. Bei Verwendung dieses Formulars wird eine neue Spalte hinzugefügt, und die vorhandene wird beibehalten.
* *Name:* Ein Name für die neue Spalte.
* *Typname:* Gibt den zugrunde liegenden Typ der Elemente des Arrays an, der zum Typ der Spalte wird, die vom `mv-expand`-Operator generiert wird. Der Vorgang zum Anwenden des Typs ist nur Umwandlung und umfasst keine Analyse oder Typkonvertierung. Arrayelemente, die nicht mit dem deklarierten Typ übereinstimmen, werden zu `null`-Werten.
* *RowLimit:* Die maximale Anzahl von Zeilen, die aus jeder ursprünglichen Zeile generiert werden. Der Standardwert ist 2147483647. 

  > [!NOTE]
  > `mvexpand` ist eine Legacy- und veraltete Form des Operators `mv-expand`. Die Legacyversion weist ein Standardzeilenlimit von 128 auf.

* *IndexColumnName:* Wenn `with_itemindex` angegeben wird, enthält die Ausgabe eine zusätzliche Spalte (mit dem Namen *IndexColumnName*), die den Index (beginnend bei 0) des Elements in der ursprünglichen erweiterten Sammlung enthält. 

## <a name="returns"></a>Rückgabe

Mehrere Zeilen für alle Werte in jedem Array, die sich in der benannten Spalte oder im Arrayausdruck befinden.
Wenn mehrere Spalten oder Ausdrücke angegeben werden, werden diese parallel erweitert. Für jede Eingabezeile gibt es so viele Ausgabezeilen, wie Elemente im längsten erweiterten Ausdruck vorhanden sind (kürzere Listen werden mit Nullen aufgefüllt). Wenn der Wert in einer Zeile ein leeres Array ist, wird die Zeile zu nichts erweitert (wird im Resultset nicht angezeigt). Wenn der Wert in einer Zeile jedoch kein Array ist, wird die Zeile unverändert im Resultset beibehalten. 

Die erweiterte Spalte ist immer dynamisch typisiert. Verwenden Sie eine Umwandlung wie `todatetime()` oder `tolong()`, wenn Sie Werte berechnen oder aggregieren möchten.

Zwei Erweiterungsmodi für Eigenschaftenbehälter werden unterstützt:
* `bagexpansion=bag` oder `kind=bag`: Eigenschaftenbehälter werden zu Eigenschaftenbehältern mit einem einzelnen Eintrag erweitert. Bei diesem Modus handelt es sich um die Standarderweiterung.
* `bagexpansion=array` oder `kind=array`: Eigenschaftenbehälter werden zu Arraystrukturen mit den beiden Elementen `[`*Schlüssel*`,`*Wert*`]` erweitert und lassen den einheitlichen Zugriff auf Schlüssel und Werte zu (sowie z. B. auch das Ausführen einer distinct-count-Aggregation über Eigenschaftsnamen). 

## <a name="examples"></a>Beispiele

### <a name="single-column"></a>Einzelne Spalte

Eine einfache Erweiterung einer einzelnen Spalte:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|

### <a name="zipped-two-columns"></a>Zippen zweier Spalten

Wenn Sie zwei Spalten erweitern, werden die entsprechenden Spalten zuerst „gezippt“ und dann erweitert:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>Kartesisches Produkt von zwei Spalten

Wenn Sie ein kartesisches Produkt des Erweiterns von zwei Spalten erhalten möchten, erweitern Sie ein Spalte nach der anderen:

<!-- csl: https://kuskusdfv3.kusto.windows.net/Kuskus -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)
  [
  1,
  dynamic({"prop1":"a", "prop2":"b"}),
  dynamic([5, 6])
  ]
| mv-expand b
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{  "prop1": "a"}|5|
|1|{  "prop1": "a"}|6|
|1|{  "prop2": "b"}|5|
|1|{  "prop2": "b"}|6|

### <a name="convert-output"></a>Ausgabe konvertieren

Wenn Sie die Ausgabe von mv-expand für einen bestimmten Typ (standardmäßig dynamisch) erzwingen möchten, verwenden Sie `to typeof`:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:string, b:dynamic, c:dynamic)["Constant", dynamic([1,2,3,4]), dynamic([6,7,8,9])]
| mv-expand b, c to typeof(int)
| getschema 
```

ColumnName|ColumnOrdinal|DateType|ColumnType
-|-|-|-
a|0|System.String|Zeichenfolge
b|1|System.Object|dynamisch
c|2|System.Int32|INT

Beachten Sie, dass Spalte `b` als `dynamic` ausgegeben wird, während `c` als `int` ausgegeben wird.

### <a name="using-with_itemindex"></a>Verwenden von with_itemindex

Erweitern eines Arrays mit `with_itemindex`:

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

* Weitere Beispiele finden Sie unter [Diagrammanzahl von Liveaktivitäten im Lauf der Zeit](./samples.md#chart-concurrent-sessions-over-time).
* [mv-apply](./mv-applyoperator.md)-Operator
* [summarize make_list()](makelist-aggfunction.md), die umgekehrte Funktion von mv-expand.
* [bag_unpack()](bag-unpackplugin.md)-Plug-In für die Erweiterung dynamischer JSON-Objekte in Spalten mithilfe von Eigenschaftenbehälterschlüsseln.
