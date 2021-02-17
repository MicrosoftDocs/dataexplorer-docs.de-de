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
ms.openlocfilehash: 956c24fa70df89f5bf99d4de12a8b07da6cb6912
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359980"
---
# <a name="mv-expand-operator"></a>mv-expand-Operator

Erweitert dynamische Arrays oder Eigenschaftenbehälter mit mehreren Werten auf mehrere Datensätze.

`mv-expand` kann als Gegenteil der Aggregationsoperatoren beschrieben werden, die mehrere Werte in einem einzelnen Array oder Eigenschaftenbehälter des Typs [dynamisch](./scalar-data-types/dynamic.md) packen, z. B. `summarize` ... `make-list()` und `make-series`.
Jedes Element im (skalaren) Array oder Eigenschaftenbehälter generiert einen neuen Datensatz in der Ausgabe des Operators. Alle nicht erweiterten Spalten der Eingabe werden in alle Datensätze in der Ausgabe dupliziert.

## <a name="syntax"></a>Syntax

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`to typeof(` *Typename*`)`] [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] *Name* `=` *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>Argumente

* *ColumnName*, *ArrayExpression*: Ein Spaltenverweis oder ein skalarer Ausdruck mit einem Wert vom Typ `dynamic`, der ein Array oder einen Eigenschaftenbehälter enthält. Die einzelnen Elemente der obersten Ebene des Arrays oder Eigenschaftenbehälters werden auf mehrere Datensätze erweitert.<br>
  Wenn *ArrayExpression* verwendet wird und *Name* keinem Eingabespaltennamen entspricht, wird der erweiterte Wert auf eine neue Spalte in der Ausgabe erweitert.
  Andernfalls wird der vorhandene *ColumnName*-Wert ersetzt.

* *Name:* Ein Name für die neue Spalte.

* *Typname:* Gibt den zugrunde liegenden Typ der Elemente des Arrays an, der zum Typ der Spalte wird, die vom `mv-expand`-Operator generiert wird. Der Vorgang zum Anwenden des Typs ist nur Umwandlung und umfasst keine Analyse oder Typkonvertierung. Arrayelemente, die nicht mit dem deklarierten Typ übereinstimmen, werden zu `null`-Werten.

* *RowLimit:* Die maximale Anzahl von Zeilen, die aus jeder ursprünglichen Zeile generiert werden. Der Standardwert ist 2147483647. 

  > [!NOTE]
  > `mvexpand` ist eine Legacy- und veraltete Form des Operators `mv-expand`. Die Legacyversion weist ein Standardzeilenlimit von 128 auf.

* *IndexColumnName:* Wenn `with_itemindex` angegeben wird, enthält die Ausgabe eine weitere Spalte (mit dem Namen *IndexColumnName*), die den Index (beginnend bei 0) des Elements in der ursprünglichen erweiterten Sammlung enthält. 

## <a name="returns"></a>Gibt zurück

Der Operator gibt für jeden Datensatz in der Eingabe null, einen oder mehrere Datensätze in der Ausgabe zurück, wie auf folgende Weise festgelegt:

1. Nicht erweiterte Eingabespalten werden in der Ausgabe mit ihrem ursprünglichen Wert angezeigt.
   Wird ein einzelner Eingabedatensatz auf mehrere Ausgabedatensätze erweitert, wird der Wert in alle Datensätze dupliziert.

1. Für jeden erweiterten *ColumnName*- oder *ArrayExpression*-Wert wird die Anzahl von Ausgabedatensätzen für jeden Wert wie [weiter unten](#modes-of-expansion) beschrieben bestimmt. Für jeden Eingabedatensatz wird die maximale Anzahl von Ausgabedatensätzen berechnet. Alle Arrays oder Eigenschaftenbehälter werden parallel erweitert, sodass fehlende Werte (sofern vorhanden) durch NULL-Werte ersetzt werden.

1. Ist der dynamische Wert NULL, wird ein einzelner Datensatz für diesen Wert erstellt (NULL).
   Ist der dynamische Wert ein leeres Array oder ein leerer Eigenschaftenbehälter, wird kein Datensatz für diesen Wert erstellt.
   Andernfalls werden so viele Datensätze erstellt, wie es Elemente im dynamischen Wert gibt.

Die erweiterten Spalten sind vom Typ `dynamic`, es sei denn, sie werden explizit mit der Klausel `to typeof()` typisiert.

### <a name="modes-of-expansion"></a>Erweiterungsmodi

Zwei Erweiterungsmodi für Eigenschaftenbehälter werden unterstützt:

* `bagexpansion=bag` oder `kind=bag`: Eigenschaftenbehälter werden zu Eigenschaftenbehältern mit einem einzelnen Eintrag erweitert. Dieser Modus ist der Standardmodus.
* `bagexpansion=array` oder `kind=array`: Eigenschaftenbehälter werden zu Arraystrukturen mit den beiden Elementen `[`*Schlüssel*`,`*Wert*`]` erweitert und lassen den einheitlichen Zugriff auf Schlüssel und Werte zu. Dieser Modus ermöglicht beispielsweise auch das Ausführen einer distinct-count-Aggregation über Eigenschaftsnamen. 

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

Beachten Sie, dass die Spalte `b` als `dynamic` zurückgegeben wird, während `c` als `int`zurückgegeben wird.

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
