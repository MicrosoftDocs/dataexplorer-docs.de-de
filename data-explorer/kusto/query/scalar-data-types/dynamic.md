---
title: Der dynamische Datentyp - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der dynamische Datentyp in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e1cdb6e5af20b326198a7447c50c24e5f632d237
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509876"
---
# <a name="the-dynamic-data-type"></a>Der dynamische Datentyp

Der `dynamic` skalare Datentyp ist insofern besonders, als er einen beliebigen Wert anderer skalarer Datentypen aus der Liste unten sowie Arrays und Eigenschaftssäcke annehmen kann. Ein `dynamic` Wert kann insbesondere sein:

* Null.
* Ein Wert eines der primitiven skalaren `bool` `datetime`Datentypen: , `string`, `timespan` `guid`, `int` `long`, `real`, , , und .
* Ein Array `dynamic` von Werten, das null oder mehr Werte mit nullbasierter Indizierung hält.
* Eine Eigenschaftstasche, `string` die `dynamic` eindeutige Werte Werten zuordnet.
  Der Eigenschaftenbeutel hat null oder mehr solcher Zuordnungen (sogenannte `string` "Slots"), die durch die eindeutigen Werte indiziert werden. Die Slots sind ungeordnet.

> [!NOTE]
> Die Werte `dynamic` des Typs sind auf 1 MB (2 x 20) begrenzt.

> [!NOTE]
> Obwohl `dynamic` der Typ JSON-like erscheint, kann er Werte enthalten, die das JSON-Modell nicht darstellt, `long` `real`da `datetime` `timespan`sie `guid`in JSON nicht vorhanden sind (z. B. , , , , und ).
> Daher werden beim `dynamic` Serialisieren von Werten in eine JSON-Darstellung Werte, `string` die JSON nicht darstellen kann, in Werte serialisiert. Umgekehrt analysiert Kusto Zeichenfolgen als stark typisierte Werte, wenn sie als solche analysiert werden können.
> Dies `datetime`gilt `real` `long`für `guid` , , und Typen. Weitere Informationen zum JSON-Objektmodell finden Sie unter [json.org](https://json.org/).

> [!NOTE]
> Kusto versucht nicht, die Reihenfolge der Name-zu-Wert-Zuordnungen in einem Eigenschaftenbeutel beizubehalten, und Sie können daher nicht davon ausgehen, dass die Reihenfolge beibehalten werden soll. Es ist durchaus möglich, dass zwei Eigenschaftssäcke mit demselben Satz von `string` Zuordnungen unterschiedliche Ergebnisse liefern, wenn sie z. B. als Werte dargestellt werden.

## <a name="dynamic-literals"></a>Dynamische Literale

Ein Literal `dynamic` des Typs sieht wie folgt aus:

`dynamic(`*Wert*`)`

*Wert* kann sein:

* `null`, in diesem Fall stellt das `dynamic(null)`Literal den dynamischen NULLwert dar: .
* Ein weiteres skalares Datentypliteral, in `dynamic` diesem Fall stellt das Literal das Literal des Typs "inner" dar. Beispielsweise ist `dynamic(4)` ein dynamischer Wert, der den Wert 4 des langen skalaren Datentyps enthält.
* Ein Array dynamischer oder `[` anderer Literale: *ListOfValues* `]`. Beispielsweise `dynamic([1, 2, "hello"])` handelt es sich um ein `long` dynamisches `string` Array aus drei Elementen, zwei Werten und einem Wert.
* Eine Immobilientasche: `{` *Namenswert* `=` *Value* ... `}`. Beispielsweise `dynamic({"a":1, "b":{"a":2}})` handelt es sich um eine `a`Eigenschaftstasche mit zwei Steckplätzen , und `b`, wobei der zweite Steckplatz eine weitere Eigenschaftstasche ist.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

Der Einfachheit `dynamic` halber können Literale, die im Abfragetext selbst angezeigt `datetime` werden, `timespan` auch andere Kusto-Literale (z. B. Literale, Literale usw.) enthalten. Diese Erweiterung über JSON ist beim Analysieren von Zeichenfolgen (z. B. bei verwendung der `parse_json` Funktion oder beim Übertragen von Daten) nicht verfügbar, aber sie ermöglicht Ihnen dies:

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

Um einen `string` Wert, der den JSON-Codierungsregeln folgt, in einen `dynamic` Wert zu analysieren, verwenden Sie die `parse_json` Funktion. Beispiel:

* `parse_json('[43, 21, 65]')` : ein Array mit Zahlen
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`- ein Wörterbuch
* `parse_json('21')` : ein einzelner Wert vom Typ „dynamic“ mit einer Zahl
* `parse_json('"21"')` : ein einzelner Wert vom Typ „dynamic“ mit einer Zeichenfolge
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`- gibt den `o` gleichen Wert wie im obigen Beispiel.

> [!NOTE]
> Im Gegensatz zu JavaScript schreibt JSON die`"`Verwendung von Doppelanführungszeichen ( ) um Zeichenfolgen und Eigenschaftsnamen vor.
> Daher ist es im Allgemeinen einfacher, ein JSON-codiertes Zeichenfolgenliteral mit einem Single-Quote -`'`) -Zeichen zu zitieren.
  
Das folgende Beispiel zeigt, wie Sie `dynamic` eine Tabelle definieren `datetime` können, die eine Spalte (sowie eine Spalte) enthält, und dann einen einzelnen Datensatz darin aufnehmen können. Außerdem wird veranschaulicht, wie Sie JSON-Zeichenfolgen in CSV-Dateien kodieren können:

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
|2015-01-01 00:00:00.0000000 | "EventType":"Demo","EventValue":"Doppelte Quote Liebe!"|

## <a name="dynamic-object-accessors"></a>Dynamische Objektaccessoren

Um ein Wörterbuch zu unterschreiben,`dict.key`verwenden Sie entweder die`dict["key"]`Punktnotation ( ) oder die Klammern notation ( ).
Wenn es sich bei dem Subskript um eine Zeichenfolgenkonstante handelt, sind beide Optionen äquivalent.

> [!NOTE] 
> Um einen Ausdruck als Subskript zu verwenden, verwenden Sie die Klammernnotation. Bei verwendungsarithmetischen Ausdrücken muss der Ausdruck innerhalb der Klammern in Klammern eingeschlossen werden.

In den `dict` folgenden `arr` Beispielen und sind Spalten des dynamischen Typs:

|Ausdruck                        | Accessor-Ausdruckstyp | Bedeutung                                                                              | Kommentare                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | Entitätsname (Spalte)     | Subskriptiiert ein Wörterbuch mit `col` den Werten der Spalte als Schlüssel              | Spalte muss vom Typ string sein                 | 
|arr[index]                        | Entitätsindex (Spalte)    | Subskriptiiert ein Array unter `index` Verwendung der Werte der Spalte als Index              | Die Spalte muss vom Typ Integer oder boolesche     | 
|arr[-index]                       | Entitätsindex (Spalte)    | Ruft den 'index'-ten Wert vom Ende des Arrays ab                             | Die Spalte muss vom Typ Integer oder boolesche     |
|arr[(-1)]                         | Entitätsindex             | Ruft den letzten Wert im Array ab                                                |                                               |
|arr[toint(indexAsString)]         | Funktionsaufruf             | Gibt die Werte `indexAsString` der Spalte in int um und verwendet sie, um ein Array zu subskriptieren |                                               |
|dik[['where']]                   | Schlüsselwort, das als Entitätsname verwendet wird (Spalte) | Subskriptiatiert ein Wörterbuch `where` mit den Werten der Spalte als Schlüssel    | Entitätsnamen, die mit einigen Schlüsselwörtern in der Abfragesprache identisch sind, müssen zitiert werden. | 
|dik.['where'] oder dict['where']   | Konstante                 | Subskriptiatiert `where` ein Wörterbuch mit Zeichenfolge als Schlüssel                              |                                               |

**Leistungstipp:** Bevorzugen Sie konstante Subskriptionen, wenn möglich

Der Zugriff auf ein `dynamic` Unterobjekt eines `dynamic` Werts ergibt einen anderen Wert, auch wenn das Unterobjekt einen anderen zugrunde liegenden Typ hat. Verwenden `gettype` Sie die Funktion, um den tatsächlichen zugrunde liegenden Typ des Werts zu ermitteln, und eine der unten aufgeführten Umwandlungsfunktionen, um ihn in den tatsächlichen Typ umzuteilen.

## <a name="casting-dynamic-objects"></a>Gießen dynamischer Objekte

> Nachdem Sie ein dynamisches Objekt subskriptisiert haben, müssen Sie den Wert in einen einfachen Typ umlegen.

|Ausdruck | Wert | type|
|---|---|---|
| X | parse_json('[100,101,102]')| array|
|X[0]|parse_json('100')|dynamisch|
|toint(X[1])|101| INT|
| J | parse_json('''a1':100, "a b c":"2015-01-01"')| dictionary|
|Y.a1|parse_json('100')|dynamisch|
|Y["a b c"]| parse_json("2015-01-01")|dynamisch|
|todate(Y["a b c"])|datum(2015-01-01)| datetime|

Cast-Funktionen sind:

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>Erstellen dynamischer Objekte

Mit mehreren Funktionen können `dynamic` Sie neue Objekte erstellen:

* [pack()](../packfunction.md) erstellt einen Eigenschaftsbeutel aus Name/Wert-Paaren.
* [pack_array()](../packarrayfunction.md) erstellt ein Array aus Name/Wert-Paaren.
* [range()](../rangefunction.md) erstellt ein Array mit einer arithmetischen Zahlenreihe.
* [zip()](../zipfunction.md) paart "parallele" Werte aus zwei Arrays zu einem einzigen Array.
* [repeat()](../repeatfunction.md) erstellt ein Array mit einem wiederholten Wert.

Darüber hinaus gibt es mehrere `dynamic` Aggregatfunktionen, die Arrays erstellen, die aggregierte Werte enthalten:

* [buildschema()](../buildschema-aggfunction.md) gibt das Aggregatschema mehrerer `dynamic` Werte zurück.
* [make_bag()](../make-bag-aggfunction.md) gibt einen Eigenschaftensack mit dynamischen Werten innerhalb der Gruppe zurück.
* [make_bag_if()](../make-bag-if-aggfunction.md) gibt einen Eigenschaftensack mit dynamischen Werten innerhalb der Gruppe zurück (mit einem Prädikat).
* [make_list()](../makelist-aggfunction.md) gibt ein Array zurück, das alle Werte nacheinander hält.
* [make_list_if()](../makelistif-aggfunction.md) gibt ein Array zurück, das alle Werte in der Reihenfolge (mit einem Prädikat) hält.
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) gibt ein Array zurück, das alle Werte in der Reihenfolge, einschließlich NULL-Werten, hält.
* [make_set()](../makeset-aggfunction.md) gibt ein Array zurück, das alle eindeutigen Werte hält.
* [make_set_if()](../makesetif-aggfunction.md) gibt ein Array zurück, das alle eindeutigen Werte (mit einem Prädikat) hält.

## <a name="operators-and-functions-over-dynamic-types"></a>Operatoren und Funktionen auf dynamischen Typen

|||
|---|---|
| *value* `in` *array*| TRUE, wenn ein Element von *array* vorhanden ist, das *value* entspricht<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| TRUE, wenn kein Element von *array* vorhanden ist, das *value* entspricht
|[`array_length(`array`)`](../arraylengthfunction.md)| Null, wenn es sich nicht um ein Array handelt.
|[`bag_keys(`Tasche`)`](../bagkeysfunction.md)| Zählt alle Stammschlüssel in einem dynamischen Property-Bag-Objekt auf.
|[`extractjson(`pfad,Objekt`)`](../extractjsonfunction.md)|Verwendet den Pfad zum Navigieren in das Objekt.
|[`parse_json(`Quelle`)`](../parsejsonfunction.md)| Wandelt eine JSON-Zeichenfolge in ein dynamisches Objekt um.
|[`range(`von,to,schritt`)`](../rangefunction.md)| Ein Array von Werten
|[`mv-expand`listColumn](../mvexpandoperator.md) | Repliziert eine Zeile für jeden Wert in einer Liste in eine angegebene Zelle.
|[`summarize buildschema(`Spalte`)`](../buildschema-aggfunction.md) |Leitet das Typschema aus dem Spalteninhalt ab.
|[`summarize make_bag(`Spalte`)`](../make-bag-aggfunction.md) | Führt die Eigenschaftsbeutelwerte (Wörterbuchwerte) in der Spalte in einer Eigenschaftstasche zusammen, ohne dass die Schlüsselduplizierung zu verwenden ist.
|[`summarize make_bag_if(`Spalte,Prädikat`)`](../make-bag-if-aggfunction.md) | Führt die Eigenschaftsbeutelwerte (Wörterbuchwerte) in der Spalte in einem Eigenschaftenbeutel zusammen, ohne Schlüsselduplizierung (mit Prädikat).
|[`summarize make_list(`Spalte`)`](../makelist-aggfunction.md)| Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array.
|[`summarize make_list_if(`Spalte,Prädikat`)`](../makelistif-aggfunction.md)| Flattens Gruppen von Zeilen und legt die Werte der Spalte in einem Array (mit Prädikat).
|[`summarize make_list_with_nulls(`Spalte`)`](../make-list-with-nulls-aggfunction.md)| Verflacht Gruppen von Zeilen und legt die Werte der Spalte in ein Array, einschließlich NULL-Werte, ab.
|[`summarize make_set(`Spalte`)`](../makeset-aggfunction.md) | Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array ohne Duplizierung.
|[`summarize make_bag(`Spalte`)`](../make-bag-aggfunction.md) | Führt die Eigenschaftsbeutelwerte (Wörterbuchwerte) in der Spalte in einer Eigenschaftstasche zusammen, ohne dass die Schlüsselduplizierung zu verwenden ist.