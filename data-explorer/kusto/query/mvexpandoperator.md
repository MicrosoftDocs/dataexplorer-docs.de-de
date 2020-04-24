---
title: mv-expand-Operator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den mv-expand-Operator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: 01a4b56155d1c29727670b4e827cb7b9968ef7a9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512290"
---
# <a name="mv-expand-operator"></a>mv-expand-Operator

Erweitert mehrwertiges Array oder Eigenschaftenbeutel.

`mv-expand`wird auf eine [dynamische](./scalar-data-types/dynamic.md)-typisierte Spalte angewendet, sodass jeder Wert in der Auflistung eine separate Zeile erhält. Alle anderen Spalten in einer erweiterten Zeile werden dupliziert. 

**Syntax**

*T* `| mv-expand ` `bagexpansion=`[`bag` | `array`(`with_itemindex=`)] [*IndexColumnName*] *Spaltenname* [`,` *Spaltenname* ...] [`limit` *Zeilenlimit*]

*T* `| mv-expand ` `bagexpansion=`[`bag` | `array`( )] [`to typeof(`*Name* `=`] *ArrayExpression* [*Typname*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typname*`)`] ...] [`limit` *Zeilenlimit*]

**Argumente**

* *ColumnName:* Im Ergebnis werden Arrays in der benannten Spalte auf mehrere Zeilen erweitert. 
* *ArrayExpression:* Ein Ausdruck, der ein Array zurückgibt. Bei Verwendung dieses Formulars wird eine neue Spalte hinzugefügt, und die vorhandene wird beibehalten.
* *Name:* Ein Name für die neue Spalte.
* *Typname:* Gibt den zugrunde liegenden Typ der Arrayelemente an, der zum Typ der vom Operator erstellten Spalte wird.
    Beachten Sie, dass Werte im Array, die diesem Typ nicht entsprechen, nicht konvertiert werden. vielmehr werden sie einen `null` Wert annehmen.
* *RowLimit:* Die maximale Anzahl von Zeilen, die aus jeder ursprünglichen Zeile generiert werden. Der Standardwert ist 2147483647. 
*Hinweis:* Die ältere und `mvexpand` veraltete Form des Operators hat das standardmäßige Zeilenlimit 128.
* *IndexColumnName:* Wenn `with_itemindex` angegeben, enthält die Ausgabe eine zusätzliche Spalte (mit dem Namen *IndexColumnName*), die den Index (ab 0) des Elements in der ursprünglichen erweiterten Auflistung enthält. 

**Rückgabe**

Mehrere Zeilen für alle Werte in jedem Array in der benannten Spalte oder im Arrayausdruck.
Wenn mehrere Spalten oder Ausdrücke angegeben werden, werden sie parallel erweitert, sodass für jede Eingabezeile so viele Ausgabezeilen vorhanden sind, wie Elemente im längsten erweiterten Ausdruck vorhanden sind (kürzere Listen werden mit NULLs aufgepolstert). Wenn der Wert in einer Zeile ein leeres Array ist, wird die Zeile zu nichts erweitert (wird im Resultset nicht angezeigt). Wenn der Wert in einer Zeile kein Array ist, wird die Zeile wie in der Ergebnismenge beibehalten. 

Die erweiterte Spalte ist immer dynamisch typisiert. Verwenden Sie eine Umwandlung wie `todatetime()` oder `tolong()`, wenn Sie Werte berechnen oder aggregieren möchten.

Zwei Erweiterungsmodi für Eigenschaftenbehälter werden unterstützt:
* `bagexpansion=bag`: Eigenschaftenbehälter werden zu Eigenschaftenbehältern mit einem einzelnen Eintrag erweitert. Dies ist die Standarderweiterung.
* `bagexpansion=array`: Eigenschaftssäcke werden in Zwei-Elemente-Schlüsselwert-Array-Strukturen `[` *key*`,`*value* `]` erweitert, die einen einheitlichen Zugriff auf Schlüssel und Werte ermöglichen (sowie z. B. eine Aggregation mit unterschiedlicher Anzahl über Eigenschaftsnamen ausführen). 

**Beispiele**

Eine einfache Erweiterung einer einzelnen Spalte:
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|"prop1":"a"|
|1|"prop2":"b"|


Wenn Sie zwei Spalten erweitern, werden die entsprechenden Spalten zuerst "zip" und dann erweitert:

```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|"prop1":"a"|5|
|1|"prop2":"b"||

Wenn Sie ein kartesisches Produkt zum Erweitern von zwei Spalten erhalten möchten, erweitern Sie eine nach der anderen:
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|"prop1":"a"|5|
|1|"prop2":"b"|5|


Erweiterung eines Arrays mit: `with_itemindex`
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

Siehe [Diagrammanzahl der Live-Aktivitäten im Zeitverlauf](./samples.md#concurrent-activities).

**Siehe auch**

- [mv-apply-Operator.](./mv-applyoperator.md)
- [fassen Sie make_list(),](makelist-aggfunction.md) die die entgegengesetzte Funktion ausführt.
- [bag_unpack()](bag-unpackplugin.md) Plugin zum Erweitern dynamischer JSON-Objekte in Spalten mithilfe von Eigenschaftenbeutelschlüsseln.