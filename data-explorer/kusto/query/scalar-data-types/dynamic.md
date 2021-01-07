---
title: Der Datentyp „dynamic“ – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird der Datentyp „dynamic“ in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 2ef75f2249466a9796fb0313186546bd9088ff07
ms.sourcegitcommit: 28f18c3500992fd7dcfd95dffe2bae0161a22ca3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/28/2020
ms.locfileid: "97792930"
---
# <a name="the-dynamic-data-type"></a>Der Datentyp „dynamic“

Der skalare Datentyp `dynamic` ist insofern eine Besonderheit, als er jeden beliebigen Wert eines anderen skalaren Datentyps aus der nachstehenden Liste sowie ein Array oder eine Eigenschaftensammlung annehmen kann. Insbesondere kann ein `dynamic`-Wert Folgendes sein:

* Null.
* Ein Wert, der einen der primitiven skalaren Datentypen hat: `bool`, `datetime`, `guid`, `int`, `long`, `real`, `string` oder `timespan`.
* Ein Array mit `dynamic`-Werten, das 0 (null) oder mehr Werte enthält, wobei die Indizierung nullbasiert erfolgt.
* Eine Eigenschaftensammlung (Eigenschaftenbehälter), in der eindeutige `string`-Werte `dynamic`-Werten zugeordnet werden.
  Eine Eigenschaftensammlung enthält 0 (null) oder mehr solcher Zuordnungen (sogenannte „Slots“), die durch die eindeutigen `string`-Werte indiziert sind. Die Slots sind nicht sortiert.

> [!NOTE]
> * Ein Wert des Typs `dynamic` ist auf 1 MB (2^20) beschränkt.
> * Obwohl der Typ `dynamic` so aussieht, als sei er JSON-artig, kann er Werte enthalten, die das JSON-Modell nicht darstellt, weil sie in JSON nicht existieren(z. B. `long`, `real`, `datetime`, `timespan` und `guid`).
>   Daher werden `dynamic`-Werte, die JSON nicht darstellen kann, bei einem Serialisieren in eine JSON-Darstellung in `string`-Werte serialisiert. Umgekehrt werden Zeichenfolgen in Kusto als stark typisierte Werte analysiert, wenn sie als solche analysiert werden können.
>   Dies gilt für die Typen `datetime`, `real`, `long` und `guid`. 
>   Weitere Informationen zum JSON-Objektmodell finden Sie unter [json.org](https://json.org/).
> * In Kusto wird nicht versucht, die Reihenfolge der Name-zu-Wert-Zuordnungen in einer Eigenschaftensammlung beizubehalten, sodass Sie nicht davon ausgehen können, dass die Reihenfolge beibehalten wird. Es ist durchaus möglich, dass zwei Eigenschaftensammlungen mit demselben Satz von Zuordnungen unterschiedliche Ergebnisse liefern, wenn sie beispielsweise als `string`-Werte dargestellt werden.

## <a name="dynamic-literals"></a>Dynamische Literale

Ein Literal des Typs `dynamic` sieht wie folgt aus:

`dynamic(` *Wert* `)`

*Wert* kann Folgendes sein:

* `null`, wobei das Literal in diesem Fall den „dynamic“-Wert „null“ darstellt: `dynamic(null)`.
* Ein anderes Literal mit skalarem Datentyp, wobei das Literal in diesem Fall das `dynamic`-Literal des „inneren“ Typs darstellt. Beispielsweise ist `dynamic(4)` ein dynamischer Wert, der den Wert 4 des skalaren Datentyps „long“ enthält.
* Ein Array mit „dynamic“- oder anderen Literalen: `[` *ListeDerWerte* `]`. `dynamic([1, 2, "hello"])` ist beispielsweise ein dynamisches Array mit drei Elementen, zwei `long`-Werte und ein `string`-Wert.
* Eine Eigenschaftensammlung: `{` *Name* `=` *Wert* ... `}`. Beispielsweise ist `dynamic({"a":1, "b":{"a":2}})` eine Eigenschaftensammlung mit zwei Slots, `a` und `b`, wobei der zweite Slot eine weitere Eigenschaftensammlung ist.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

Zur einfacheren Verarbeitung können `dynamic`-Literale, die im Abfragetext selbst enthalten sind, auch andere Kusto-Literale enthalten, die einen der folgenden Typen haben: `datetime`, `timespan`, `real`, `long`, `guid`, `bool` oder `dynamic`.
Diese Erweiterung gegenüber JSON ist nicht verfügbar, wenn Zeichenfolgen analysiert werden (z. B. wenn die `parse_json`-Funktion verwendet wird oder Daten erfasst werden), ermöglicht Ihnen jedoch Folgendes:

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

Um einen `string`-Wert, der den JSON-Codierungsregeln entspricht, in einen `dynamic`-Wert zu analysieren, verwenden Sie die `parse_json`-Funktion. Zum Beispiel:

* `parse_json('[43, 21, 65]')` : ein Array mit Zahlen
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`: ein Wörterbuch
* `parse_json('21')` : ein einzelner Wert vom Typ „dynamic“ mit einer Zahl
* `parse_json('"21"')` : ein einzelner Wert vom Typ „dynamic“ mit einer Zeichenfolge
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`: führt zum selben Wert wie `o` im obigen Beispiel.

> [!NOTE]
> Anders als in JavaScript ist es in JSON erforderlich, Zeichenfolgen sowie die Namen der Eigenschaften in einer Eigenschaftensammlung in doppelte Anführungszeichen (`"`) zu setzen.
> Daher ist es grundsätzlich einfacher, ein JSON-codiertes Zeichenfolgenliteral in einfache Anführungszeichen (`'`) zu setzen.
  
Im folgenden Beispiel wird gezeigt, wie Sie eine Tabelle definieren können, die eine `dynamic`-Spalte (sowie eine `datetime`-Spalte) enthält, und dann einen einzelnen Datensatz in der Tabelle erfassen können. Außerdem wird veranschaulicht, wie Sie JSON-Zeichenfolgen in CSV-Dateien codieren können:

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would be:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allowed before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents.
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|Timestamp                   | Trace                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## <a name="dynamic-object-accessors"></a>Dynamische Objektaccessoren

Um ein Wörterbuch zu indizieren, verwenden Sie entweder die Punktnotation (`dict.key`) oder die Notation mit eckigen Klammern (`dict["key"]`).
Wenn der Index eine Zeichenfolgenkonstante ist, sind beide Optionen gleichwertig.

> [!NOTE] 
> Wenn Sie einen Ausdruck als Index verwenden möchten, verwenden Sie die Notation mit eckigen Klammern. Wenn arithmetische Ausdrücke verwendet werden, muss der Ausdruck in den eckigen Klammern in runde Klammern eingeschlossen sein.

In den folgenden Beispielen sind `dict` und `arr` Spalten, die den Typ „dynamic“ haben:

|Ausdruck                        | Typ des Accessorausdrucks | Bedeutung                                                                              | Kommentare                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | Entitätsname (Spalte)     | Indiziert ein Wörterbuch, wobei die Werte in der Spalte `col` als Schlüssel verwendet werden              | Die Spalte muss den Typ „string“ haben.                 | 
|arr[index]                        | Entitätsindex (Spalte)    | Indiziert ein Array, wobei die Werte in der Spalte `index` als Index verwendet werden              | Die Spalte muss den Typ „integer“ oder „boolean“ haben.     | 
|arr[-index]                       | Entitätsindex (Spalte)    | Ruft den „index“-ten Wert ab dem Ende des Arrays ab                             | Die Spalte muss den Typ „integer“ oder „boolean“ haben.     |
|arr[(-1)]                         | Entitätsindex             | Ruft den letzten Wert im Array ab                                                |                                               |
|arr[toint(indexAsString)]         | Funktionsaufruf            | Wandelt die Werte, die in der Spalte `indexAsString` stehen, in „int“-Werte um und verwendet diese, um ein Array zu indizieren |                                               |
|dict[['where']]                   | Schlüsselwort, das als Entitätsname verwendet wird (Spalte) | Indiziert ein Wörterbuch, wobei die Werte in der Spalte `where` als Schlüssel verwendet werden    | Ein Entitätsname, der mit einem Schlüsselwort der Abfragesprache identisch ist, muss in Anführungszeichen gesetzt werden. | 
|dict.['where'] oder dict['where']   | Konstante                 | Indiziert ein Wörterbuch, wobei die `where`-Zeichenfolge als Schlüssel verwendet wird                              |                                               |

**Leistungstipp:** Versuchen Sie nach Möglichkeit, konstante Indizes zu verwenden.

Ein Zugriff auf ein Unterobjekt eines `dynamic`-Werts ergibt einen anderen `dynamic`-Wert, auch wenn das untergeordnete Objekt einen anderen zugrunde liegenden Typ hat. Verwenden Sie die `gettype`-Funktion, um den tatsächlichen zugrunde liegenden Typ des Werts zu ermitteln, und eine der weiter unten aufgeführten Umwandlungsfunktionen, um den Wert in den tatsächlichen Typ umzuwandeln.

## <a name="casting-dynamic-objects"></a>Umwandeln von „dynamic“-Objekten

> Nachdem Sie ein „dynamic“-Objekt indiziert haben, müssen Sie den Wert in einen einfachen Typ umwandeln.

|Ausdruck | Wert | type|
|---|---|---|
| X | parse_json('[100,101,102]')| array|
|X[0]|parse_json('100')|dynamisch|
|toint(X[1])|101| INT|
| Y | parse_json('{"a1":100, "a b c":"2015-01-01"}')| dictionary|
|Y.a1|parse_json('100')|dynamisch|
|Y["a b c"]| parse_json("2015-01-01")|dynamisch|
|todate(Y["a b c"])|datetime(2015-01-01)| datetime|

Umwandlungsfunktionen sind:

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>Erstellen von „dynamic“-Objekten

Es gibt mehrere Funktionen, mit denen Sie neue `dynamic`-Objekte erstellen können:

* [pack()](../packfunction.md) erstellt eine Eigenschaftensammlung aus Name/Wert-Paaren.
* [pack_array()](../packarrayfunction.md) erstellt ein Array aus Name/Wert-Paaren.
* [range()](../rangefunction.md) erstellt ein Array mit einer arithmetischen Reihe von Zahlen.
* [zip()](../zipfunction.md) kombiniert „parallele“-Werte aus zwei Arrays zu einem einzigen Array.
* [repeat()](../repeatfunction.md) erstellt ein Array mit einem wiederholten Wert.

Außerdem gibt es mehrere Aggregatfunktionen, die `dynamic`-Arrays erstellen, die aggregierte Werte enthalten:

* [buildschema()](../buildschema-aggfunction.md) gibt das Aggregatschema mehrerer `dynamic`-Werte zurück.
* [make_bag()](../make-bag-aggfunction.md) gibt eine Eigenschaftensammlung dynamischer Werte in der Gruppe zurück.
* [make_bag_if()](../make-bag-if-aggfunction.md) gibt eine Eigenschaftensammlung dynamischer Werte in der Gruppe (mit einem Prädikat) zurück.
* [make_list()](../makelist-aggfunction.md) gibt ein Array zurück, das alle Werte der Reihe nach enthält.
* [make_list_if()](../makelistif-aggfunction.md) gibt ein Array zurück, das alle Werte der Reihe nach (mit einem Prädikat) enthält.
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) gibt ein Array zurück, das alle Werte der Reihe nach, einschließlich NULL-Werten, enthält.
* [make_set()](../makeset-aggfunction.md) gibt ein Array zurück, das alle eindeutigen Werte enthält.
* [make_set_if()](../makesetif-aggfunction.md) gibt ein Array zurück, das alle eindeutigen Werte (mit einem Prädikat) enthält.

## <a name="operators-and-functions-over-dynamic-types"></a>Operatoren und Funktionen auf dynamischen Typen

Eine umfassende Liste der dynamischen Skalarfunktionen bzw. der Arrayskalarfunktionen finden Sie unter [Dynamische Funktionen/Arrayfunktionen](../scalarfunctions.md#dynamicarray-functions).

|Operator oder Funktion|Verwendung mit dynamischen Datentypen|
|---|---|
| *value* `in` *array*| TRUE, wenn ein Element von *array* vorhanden ist, das *value* entspricht<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| TRUE, wenn kein Element von *array* vorhanden ist, das *value* entspricht
|[`array_length(`array`)`](../arraylengthfunction.md)| Null, wenn es sich nicht um ein Array handelt.
|[`bag_keys(`bag`)`](../bagkeysfunction.md)| Listet alle Stammschlüssel auf, die in einer „dynamic“-Eigenschaftensammlung enthalten sind.
|[`bag_merge(`bag1,...,bagN`)`](../bag-merge-function.md)| Führt „dynamic“-Eigenschaftensammlungen in einer dynamischen Eigenschaftensammlung zusammen, wobei alle Eigenschaften zusammengeführt werden.
|[`extractjson(`path,object`)`](../extractjsonfunction.md)|Verwendet den Pfad zum Navigieren in das Objekt.
|[`parse_json(`Quelle`)`](../parsejsonfunction.md)| Wandelt eine JSON-Zeichenfolge in ein dynamisches Objekt um.
|[`range(`from,to,step`)`](../rangefunction.md)| Ein Array von Werten
|[`mv-expand` listColumn](../mvexpandoperator.md) | Repliziert eine Zeile für jeden Wert in einer Liste in eine angegebene Zelle.
|[`summarize buildschema(`Säulendiagramm`)`](../buildschema-aggfunction.md) |Leitet das Typschema aus dem Spalteninhalt ab.
|[`summarize make_bag(`Säulendiagramm`)`](../make-bag-aggfunction.md) | Führt die Eigenschaftensammlungswerte (Wörterbuchwerte) in der Spalte in einer Eigenschaftensammlung ohne Schlüsselduplizierung zusammen.
|[`summarize make_bag_if(`column,predicate`)`](../make-bag-if-aggfunction.md) | Führt die Eigenschaftensammlungswerte (Wörterbuchwerte) in der Spalte in einer Eigenschaftensammlung ohne Schlüsselduplizierung (mit Prädikat) zusammen.
|[`summarize make_list(`column`)` ](../makelist-aggfunction.md)| Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array.
|[`summarize make_list_if(`column,predicate`)` ](../makelistif-aggfunction.md)| Vereinfacht die Zeilengruppen und schreibt die Werte der Spalte in ein Array (mit Prädikat).
|[`summarize make_list_with_nulls(`column`)` ](../make-list-with-nulls-aggfunction.md)| Vereinfacht die Zeilengruppen und schreibt die Werte der Spalte in ein Array, einschließlich NULL-Werten.
|[`summarize make_set(`Säulendiagramm`)`](../makeset-aggfunction.md) | Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array ohne Duplizierung.
