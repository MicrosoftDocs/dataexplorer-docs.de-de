---
title: 'Der dynamische Datentyp: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der dynamische Datentyp in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 582683a9261d84fa24d819b5234e58effaf90a97
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512025"
---
# <a name="the-dynamic-data-type"></a>Der dynamische Datentyp.

Der `dynamic` skalare Datentyp ist ein spezieller Wert, da er einen beliebigen Wert von anderen skalaren Datentypen aus der nachstehenden Liste sowie Arrays und Eigenschaften Behälter annehmen kann. Ein `dynamic` Wert kann wie folgt lauten:

* Normal.
* Ein Wert eines beliebigen primitiven skalaren Datentyps: `bool` , `datetime` , `guid` , `int` , `long` , `real` , `string` und `timespan` .
* Ein Array von- `dynamic` Werten, das 0 (null) oder mehr Werte mit NULL basierter Indizierung enthält.
* Ein Eigenschaften Behälter, der Werte eindeutigen Werten zuordnet `string` `dynamic` .
  Der Eigenschaften Behälter weist keine oder mehrere solcher Zuordnungen (als "Slots" bezeichnet) auf, die von den eindeutigen Werten indiziert werden `string` . Die Slots sind nicht geordnet.

> [!NOTE]
> * Werte des Typs `dynamic` sind auf 1 MB (2 ^ 20) beschränkt.
> * Obwohl der `dynamic` Typ JSON-ähnlich erscheint, kann er Werte enthalten, die das JSON-Modell nicht darstellt, weil Sie nicht in JSON (z. b.,,, `long` `real` `datetime` `timespan` und `guid` ) vorhanden sind.
>   Bei der Serialisierung von `dynamic` Werten in eine JSON-Darstellung werden Werte, die JSON nicht darstellen kann, in-Werte serialisiert `string` . Umgekehrt analysiert Kusto Zeichen folgen als stark typisierte Werte, wenn Sie als solche analysiert werden können.
>   Dies gilt für `datetime` die `real` Typen,, `long` und `guid` . 
>   Weitere Informationen zum JSON-Objektmodell finden Sie unter [JSON.org](https://json.org/).
> * Kusto versucht nicht, die Reihenfolge der Name-zu-Wert-Zuordnungen in einem Eigenschaften Behälter beizubehalten, sodass Sie nicht davon ausgehen können, dass die Reihenfolge beibehalten werden muss. Es ist durchaus möglich, dass zwei Eigenschaften Behälter mit demselben Satz von Zuordnungen unterschiedliche Ergebnisse liefern, wenn Sie `string` z. b. als Werte dargestellt werden.

## <a name="dynamic-literals"></a>Dynamische Literale

Ein Literaltyp `dynamic` sieht wie folgt aus:

`dynamic(` *Wert* `)`

Der *Wert* kann wie folgt lauten:

* `null`, wobei das Literale den dynamischen NULL-Wert darstellt: `dynamic(null)` .
* Ein anderer skalare Datentyp Literale. in diesem Fall stellt das Literale das `dynamic` Literale des "inneren" Typs dar. Beispielsweise `dynamic(4)` ist ein dynamischer Wert, der den Wert 4 des langen skalaren Datentyps enthält.
* Ein Array dynamischer oder anderer Literale: `[` *listOf Values* `]` . Beispielsweise `dynamic([1, 2, "hello"])` ist ein dynamisches Array aus drei Elementen, zwei `long` Werten und einem `string` Wert.
* Ein Eigenschaften Behälter: `{` *Name* - `=` *Wert* ... `}` . Beispielsweise `dynamic({"a":1, "b":{"a":2}})` ist ein Eigenschaften Behälter mit zwei Slots, `a` und `b` , wobei der zweite Slot ein weiterer Eigenschaften Behälter ist.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

Aus Gründen `dynamic` der Lesbarkeit können Literale, die im Abfragetext selbst angezeigt werden, auch andere Kusto-Literale mit Typen enthalten: `datetime` , `timespan` , `real` , `long` , `guid` , `bool` und `dynamic` .
Diese JSON-Erweiterung ist nicht verfügbar, wenn Zeichen folgen (z. b. bei Verwendung der- `parse_json` Funktion oder beim Erfassen von Daten) verwendet werden. Sie können jedoch folgende Aktionen ausführen:

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

Verwenden Sie die-Funktion, um einen Wert zu analysieren, `string` der den JSON-Codierungsregeln in einen- `dynamic` Wert folgt `parse_json` . Beispiel:

* `parse_json('[43, 21, 65]')` : ein Array mit Zahlen
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` -ein Wörterbuch
* `parse_json('21')` : ein einzelner Wert vom Typ „dynamic“ mit einer Zahl
* `parse_json('"21"')` : ein einzelner Wert vom Typ „dynamic“ mit einer Zeichenfolge
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` -gibt denselben Wert wie `o` im obigen Beispiel an.

> [!NOTE]
> Anders als bei JavaScript schreibt JSON die Verwendung von doppelten Anführungszeichen () für Zeichen folgen und Eigenschaften Namen für Eigenschaften Behälter dar `"` .
> Daher ist es im Allgemeinen einfacher, ein JSON-codiertes Zeichenfolgenliteralzeichen mit einem einfachen Anführungs `'` Zeichen () anzugeben.
  
Im folgenden Beispiel wird gezeigt, wie Sie eine Tabelle definieren können, die eine `dynamic` Spalte (und eine `datetime` Spalte) enthält, und dann einen einzelnen Datensatz erfassen. Außerdem wird veranschaulicht, wie sie JSON-Zeichen folgen in CSV-Dateien codieren können:

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
|2015-01-01 00:00:00.0000000 | {"EventType": "Demo", "eventvalue": "Double-Quote Love!"}|

## <a name="dynamic-object-accessors"></a>Dynamische objektaccessoren

Verwenden Sie zum Abonnieren eines Wörterbuchs entweder die Punkt Notation ( `dict.key` ) oder die eckige Klammer ( `dict["key"]` ).
Wenn der Index eine Zeichen folgen Konstante ist, sind beide Optionen Äquivalent.

> [!NOTE] 
> Wenn Sie einen Ausdruck als Index verwenden möchten, verwenden Sie die Notation mit Klammern. Wenn arithmetische Ausdrücke verwendet werden, muss der Ausdruck innerhalb der Klammern in Klammern eingeschlossen werden.

In den folgenden Beispielen `dict` und `arr` sind Spalten des dynamischen Typs:

|Ausdruck                        | Accessor-Ausdruckstyp | Bedeutung                                                                              | Kommentare                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict [Col]                         | Entitäts Name (Spalte)     | Abonniert ein Wörterbuch mit den Werten der Spalte `col` als Schlüssel.              | Die Spalte muss den Typ "String" aufweisen.                 | 
|arr [index]                        | Entitäts Index (Spalte)    | Gibt ein Array mit den Werten der Spalte `index` als Index an.              | Spalte muss vom Typ "Integer" oder "booleschen" sein.     | 
|arr [-index]                       | Entitäts Index (Spalte)    | Ruft den Indexwert vom Ende des Arrays ab.                             | Spalte muss vom Typ "Integer" oder "booleschen" sein.     |
|arr [(-1)]                         | Entitäts Index             | Ruft den letzten Wert im Array ab.                                                |                                               |
|arr [zu int (indexasstring)]         | Funktionsaufruf            | Wandelt die Werte der Spalte `indexAsString` in int um und verwendet Sie, um ein Array zu indizieren. |                                               |
|dict [[' WHERE ']]                   | Als Entitäts Name verwendetes Schlüsselwort (Spalte) | Abonniert ein Wörterbuch mit den Werten der Spalte `where` als Schlüssel.    | In Anführungszeichen gesetzte Entitäts Namen müssen in Anführungszeichen eingeschlossen werden. | 
|dict. [' WHERE '] oder dict [' WHERE ']   | Konstante                 | Abonniert ein Wörterbuch mithilfe `where` der Zeichenfolge als Schlüssel.                              |                                               |

**Leistungs Tipp:** Ziehen Sie nach Möglichkeit die Verwendung konstanter Indizes vor

Der Zugriff auf ein untergeordnetes Objekt eines `dynamic` Werts ergibt einen anderen `dynamic` Wert, auch wenn das untergeordnete Objekt einen anderen zugrunde liegenden Typ aufweist. Verwenden Sie die `gettype` -Funktion, um den eigentlichen zugrunde liegenden Typ des Werts und eine der unten aufgeführten Umwandlungs Funktionen zu ermitteln, um Sie in den eigentlichen Typ umzuwandeln.

## <a name="casting-dynamic-objects"></a>Umwandeln dynamischer Objekte

> Nachdem Sie ein dynamisches Objekt abonniert haben, müssen Sie den Wert in einen einfachen Typ umwandeln.

|Ausdruck | Wert | type|
|---|---|---|
| X | parse_json ("[100101102]")| array|
|X [0]|parse_json ("100")|dynamisch|
|"zu int" (X [1])|101| INT|
| J | parse_json ("{" a1 ": 100," a b c ":" 2015-01-01 "}")| dictionary|
|Y. a1|parse_json ("100")|dynamisch|
|J ["a b c"]| parse_json ("2015-01-01")|dynamisch|
|Date (Y ["a b c"])|DateTime (2015-01-01)| datetime|

Umwandlungs Funktionen sind:

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>Dynamische Objekte werden aufgebaut.

Mehrere Funktionen ermöglichen es Ihnen, neue `dynamic` Objekte zu erstellen:

* [Pack ()](../packfunction.md) erstellt einen Eigenschaften Behälter aus Name/Wert-Paaren.
* [pack_array ()](../packarrayfunction.md) erstellt ein Array aus Name/Wert-Paaren.
* [Range ()](../rangefunction.md) erstellt ein Array mit einer arithmetischen Reihe von Zahlen.
* [ZIP ()](../zipfunction.md) kombiniert Parallele Werte von zwei Arrays in ein einzelnes Array.
* [Repeat ()](../repeatfunction.md) erstellt ein Array mit einem wiederholten Wert.

Außerdem gibt es mehrere Aggregatfunktionen, die `dynamic` Arrays erstellen, die aggregierte Werte enthalten:

* [Buildschema ()](../buildschema-aggfunction.md) gibt das aggregierte Schema mehrerer `dynamic` Werte zurück.
* [make_bag ()](../make-bag-aggfunction.md) gibt einen Eigenschaften Behälter dynamischer Werte in der Gruppe zurück.
* [make_bag_if ()](../make-bag-if-aggfunction.md) gibt einen Eigenschaften Behälter dynamischer Werte innerhalb der Gruppe (mit einem Prädikat) zurück.
* [make_list ()](../makelist-aggfunction.md) gibt ein Array zurück, das alle Werte in der Reihenfolge enthält.
* [make_list_if ()](../makelistif-aggfunction.md) gibt ein Array zurück, das alle Werte in der Sequenz (mit einem Prädikat) enthält.
* [make_list_with_nulls ()](../make-list-with-nulls-aggfunction.md) gibt ein Array zurück, das alle Werte nacheinander enthält, einschließlich NULL-Werte.
* [MAKE_SET ()](../makeset-aggfunction.md) gibt ein Array zurück, das alle eindeutigen Werte enthält.
* [make_set_if ()](../makesetif-aggfunction.md) gibt ein Array zurück, das alle eindeutigen Werte (mit einem Prädikat) enthält.

## <a name="operators-and-functions-over-dynamic-types"></a>Operatoren und Funktionen auf dynamischen Typen

|Operator oder Funktion|Verwendung mit dynamischen Datentypen|
|---|---|
| *value* `in` *array*| TRUE, wenn ein Element von *array* vorhanden ist, das *value* entspricht<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| TRUE, wenn kein Element von *array* vorhanden ist, das *value* entspricht
|[`array_length(`Array`)`](../arraylengthfunction.md)| Null, wenn es sich nicht um ein Array handelt.
|[`bag_keys(`Beut`)`](../bagkeysfunction.md)| Listet alle Stamm Schlüssel in einem dynamischen Eigenschaften Behälter Objekt auf.
|[`bag_merge(`bag1,..., Bagn`)`](../bag-merge-function.md)| Führt dynamische Eigenschaften Behälter in einem dynamischen Eigenschaften Behälter zusammen, wobei alle Eigenschaften zusammengeführt werden.
|[`extractjson(`Pfad, Objekt`)`](../extractjsonfunction.md)|Verwendet den Pfad zum Navigieren in das Objekt.
|[`parse_json(`Quelle`)`](../parsejsonfunction.md)| Wandelt eine JSON-Zeichenfolge in ein dynamisches Objekt um.
|[`range(`von, bis, Schritt`)`](../rangefunction.md)| Ein Array von Werten
|[`mv-expand` ListColumn](../mvexpandoperator.md) | Repliziert eine Zeile für jeden Wert in einer Liste in eine angegebene Zelle.
|[`summarize buildschema(`Kolumne`)`](../buildschema-aggfunction.md) |Leitet das Typschema aus dem Spalteninhalt ab.
|[`summarize make_bag(`Kolumne`)`](../make-bag-aggfunction.md) | Führt die Eigenschaften Behälter Werte (Wörterbuch) in der Spalte in einem Eigenschaften Behälter ohne Schlüssel Duplizierung zusammen.
|[`summarize make_bag_if(`Spalte, Prädikat`)`](../make-bag-if-aggfunction.md) | Führt die Eigenschaften Behälter Werte (Wörterbuch) in der Spalte zu einem Eigenschaften Behälter zusammen, ohne Schlüssel Duplizierung (mit Prädikat).
|[`summarize make_list(`Spalte `)`](../makelist-aggfunction.md)| Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array.
|[`summarize make_list_if(`Spalte, Prädikat `)`](../makelistif-aggfunction.md)| Vereinfacht Zeilen Gruppen und legt die Werte der Spalte in ein Array (mit Prädikat) ab.
|[`summarize make_list_with_nulls(`Spalte `)`](../make-list-with-nulls-aggfunction.md)| Vereinfachen Sie Zeilen Gruppen, und fügen Sie die Werte der Spalte in ein Array ein, einschließlich NULL-Werten.
|[`summarize make_set(`Kolumne`)`](../makeset-aggfunction.md) | Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array ohne Duplizierung.
