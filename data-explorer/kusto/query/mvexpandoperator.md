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
ms.openlocfilehash: f8cf59e3aa263aef224301d871a2e1e15e1a290b
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550520"
---
# <a name="mv-expand-operator"></a>mv-expand-Operator

Erweitert das Array oder den Eigenschaften Behälter mit mehreren Werten.

`mv-expand`wird auf eine [dynamisch](./scalar-data-types/dynamic.md)typisierte Spalte angewendet, sodass jeder Wert in der Auflistung eine separate Zeile erhält. Alle anderen Spalten in einer erweiterten Zeile werden dupliziert. 

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

**Beispiele**

Eine einfache Erweiterung einer einzelnen Spalte:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|k|
|---|---|
|1|{"Eigenschaft PROP1": "a"}|
|1|{"Prop2": "b"}|

Wenn Sie zwei Spalten erweitern, werden die entsprechenden Spalten zuerst "zip" und dann erweitert:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|k|c|
|---|---|---|
|1|{"Eigenschaft PROP1": "a"}|5|
|1|{"Prop2": "b"}||

Wenn Sie ein kartesisches Produkt zum Erweitern von zwei Spalten erhalten möchten, erweitern Sie eins nach dem anderen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|k|c|
|---|---|---|
|1|{"Eigenschaft PROP1": "a"}|5|
|1|{"Prop2": "b"}|5|


Erweiterung eines Arrays mit `with_itemindex` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1 
| summarize x = make_list(x) 
| mv-expand with_itemindex=Index  x 
```

|x|Index|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|


**Weitere Beispiele**

Siehe [Diagramm Anzahl der Live Aktivitäten im Zeit](./samples.md#concurrent-activities)Verlauf.

**Siehe auch**

- [MV-Apply-](./mv-applyoperator.md) Operator.
- [fassen Sie make_list ()](makelist-aggfunction.md)zusammen, der die entgegengesetzte Funktion übernimmt.
- [bag_unpack ()](bag-unpackplugin.md) -Plug-in zum Erweitern dynamischer JSON-Objekte in Spalten mithilfe von Eigenschaften Behälter Schlüsseln.
