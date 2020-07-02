---
title: 'Find-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der Such Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4be61920fe22c7b77eb54f849e86ba06a8bf533b
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763822"
---
# <a name="find-operator"></a>find-Operator

Sucht nach Zeilen, die einem Prädikat für einen Tabellen Satz entsprechen.

::: zone pivot="azuredataexplorer"

Der Bereich der `find` kann auch Daten Bank übergreifend oder Cluster übergreifend sein.

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"

find in (database('*').*) where Fruit == "apple"

find in (cluster('cluster_name').database('MyDB*'.*)) where Fruit == "apple"
```

::: zone-end

::: zone pivot="azuremonitor"

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"
```

::: zone-end

## <a name="syntax"></a>Syntax

* `find`[ `withsource` = *ColumnName*] [ `in` `(` *Tabelle* [ `,` *Tabelle*,...] `)` ] `where` *Prädikat* [ `project-smart`  |  `project` *ColumnName* [ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`,` `pack(*)`]] 

* `find`*Prädikat* [ `project-smart`  |  `project` *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`, pack(*)`]] 

## <a name="arguments"></a>Argumente

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: optional. Standardmäßig enthält die Ausgabe eine Spalte mit dem Namen *source_* , deren Werte angeben, welche Quell Tabelle die einzelnen Zeilen beigetragen hat. Wenn angegeben, wird *ColumnName* anstelle von *source_* verwendet.
Wenn die Abfrage nach Platzhalter Übereinstimmung auf Tabellen aus mehr als einer Datenbank (einschließlich der Standarddatenbank) verweist, wird für den Wert dieser Spalte ein Tabellenname mit der Datenbank qualifiziert. Entsprechend werden *Cluster* -und *Daten Bank* Qualifizierungen im Wert vorhanden sein, wenn auf mehr als ein Cluster verwiesen wird.
* *Predicate*: ein `boolean` [Ausdruck](./scalar-data-types/bool.md) für die Spalten der *Tabelle* "Eingabe Tabellen" [ `,` *Tabelle*,...]. Sie wird für jede Zeile in jeder Eingabe Tabelle ausgewertet. Weitere Informationen finden Sie unter [Prädikat Syntax Details](./findoperator.md#predicate-syntax).
* `Table`: Optional. Standardmäßig sucht die *Suche* in allen Tabellen in der aktuellen Datenbank nach:
    *  Der Name einer Tabelle, z. b.`Events`
    *  Ein Abfrageausdruck, z.B. `(Events | where id==42)`.
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise `E*` würde die Vereinigung aller Tabellen in der Datenbank bilden, deren Namen mit beginnen `E` .
* `project-smart` | `project`: Wenn nicht angegeben, `project-smart` wird standardmäßig verwendet. Weitere Informationen finden Sie unter [Details zum Ausgabe Schema](./findoperator.md#output-schema).

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: optional. Standardmäßig enthält die Ausgabe eine Spalte mit dem Namen *source_* , deren Werte angeben, welche Quell Tabelle die einzelnen Zeilen beigetragen hat. Wenn angegeben, wird *ColumnName* anstelle von *source_* verwendet.
* *Predicate*: ein `boolean` [Ausdruck](./scalar-data-types/bool.md) für die Spalten der *Tabelle* "Eingabe Tabellen" [ `,` *Tabelle*,...]. Sie wird für jede Zeile in jeder Eingabe Tabelle ausgewertet. Weitere Informationen finden Sie unter [Prädikat Syntax Details](./findoperator.md#predicate-syntax).
* `Table`: Optional. Standardmäßig *durchsucht die Suche alle* Tabellen nach:
    *  Der Name einer Tabelle, z. b.`Events` 
    *  Ein Abfrageausdruck, z.B. `(Events | where id==42)`.
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise `E*` würde die Vereinigung aller Tabellen bilden, deren Namen mit beginnen `E` .
* `project-smart` | `project`: Wenn nicht angegeben, `project-smart` wird standardmäßig verwendet. Weitere Informationen finden Sie unter [Details zum Ausgabe Schema](./findoperator.md#output-schema).

::: zone-end

## <a name="returns"></a>Gibt zurück

Transformation von Zeilen in *Tabelle* [ `,` *Tabelle*,...], für die das *Prädikat* ist `true` . Die Zeilen werden entsprechend dem [Ausgabe Schema](#output-schema)transformiert.

## <a name="output-schema"></a>Ausgabeschema

**source_ Spalte**

Die Such Operator Ausgabe enthält immer eine *source_* Spalte mit dem Namen der Quell Tabelle. Die Spalte kann mit dem-Parameter umbenannt werden `withsource` .

**Ergebnis Spalten**

Quell Tabellen, die keine Spalte enthalten, die von der Prädikat Auswertung verwendet wird, werden herausgefiltert.

Bei Verwendung von werden folgende `project-smart` Spalten in der Ausgabe angezeigt:
* Spalten, die explizit im Prädikat angezeigt werden.
* Spalten, die allen gefilterten Tabellen gemeinsam sind.

Die restlichen Spalten werden in einem Eigenschaften Behälter verpackt und in einer zusätzlichen `pack_` Spalte angezeigt.
Eine Spalte, auf die explizit durch das Prädikat verwiesen wird und die in mehreren Tabellen mit mehreren Typen angezeigt wird, verfügt im Ergebnis Schema für jeden solchen Typ über eine andere Spalte. Jeder der Spaltennamen wird aus dem ursprünglichen Spaltennamen und-Typ erstellt, getrennt durch einen Unterstrich.

Bei Verwendung von `project` *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`,` `pack(*)`]:
* Die Ergebnistabelle enthält die Spalten, die in der Liste angegeben werden. Wenn eine Quell Tabelle keine bestimmte Spalte enthält, sind die Werte in den entsprechenden Zeilen NULL.
* Wenn ein *ColumnType* -Element mit einem *ColumnName*-Wert angegeben wird, weist diese Spalte im "Ergebnis" den angegebenen Typ auf, und die Werte werden bei Bedarf in diesen Typ umgewandelt. Die Umwandlung wirkt sich nicht auf den Spaltentyp aus, wenn das *Prädikat*ausgewertet wird.
* Wenn `pack(*)` verwendet wird, werden die restlichen Spalten in einen Eigenschaften Behälter gepackt und in einer zusätzlichen `pack_` Spalte angezeigt.

**Pack_ Spalte**

Diese Spalte enthält einen Eigenschaften Behälter mit den Daten aus allen Spalten, die nicht im Ausgabe Schema angezeigt werden. Der Name der Quell Spalte dient als Eigenschaftsname, und der Spaltenwert dient als Eigenschafts Wert.

## <a name="predicate-syntax"></a>Prädikat Syntax

Der *Find* -Operator unterstützt eine alternative Syntax für den `* has` Begriff. bei Verwendung von Just *Term*wird ein Begriff über alle Eingabe Spalten hinweg durchsucht.

Eine Zusammenfassung einiger Filterfunktionen finden Sie unter [Where-Operator](./whereoperator.md).

## <a name="notes"></a>Hinweise

* Wenn die `project` -Klausel auf eine Spalte verweist, die in mehreren Tabellen angezeigt wird und mehrere Typen aufweist, muss ein Typ diesem Spalten Verweis in der Project-Klausel folgen.
* Wenn eine Spalte in mehreren Tabellen angezeigt wird und mehrere Typen aufweist und `project-smart` verwendet wird, gibt es eine entsprechende Spalte für jeden Typ im `find` Ergebnis, wie in [Union](./unionoperator.md)
* Bei der Verwendung von *Project-Intelligent*kann das Ausgabe Schema durch Änderungen im Prädikat im Quell Tabellen Satz oder im Tabellen Schema geändert werden. Wenn ein konstantes Ergebnis Schema erforderlich ist, verwenden Sie stattdessen *Project* .
* `find`der Bereich kann keine [Funktionen](../management/functions.md)enthalten. Wenn Sie eine Funktion in den Suchbereich einschließen möchten, definieren [Sie eine Let-Anweisung](./letstatement.md) mit dem View- [Schlüsselwort](./letstatement.md).

## <a name="performance-tips"></a>Leistungstipps

* Verwenden Sie [Tabellen](../management/tables.md) im Gegensatz zu [tabellarischen Ausdrücken](./tabularexpressionstatements.md).
Bei einem tabellarischen Ausdruck greift der Find-Operator auf eine Abfrage zurück, die zu einer beeinträchtigten `union` Leistung führen kann.
* Wenn eine Spalte, die in mehreren Tabellen vorhanden ist und mehrere Typen aufweist, Teil der Project-Klausel ist, empfiehlt es sich, der Project-Klausel einen *ColumnType* hinzuzufügen, um die Tabelle vor der Übergabe an zu ändern `find` .
* Fügen Sie dem Prädikat zeitbasierte Filter hinzu. Verwenden Sie einen DateTime-Spaltenwert oder [ingestion_time ()](./ingestiontimefunction.md).
* Suchen Sie in bestimmten Spalten anstelle einer Volltextsuche.
* Es ist besser, nicht auf Spalten zu verweisen, die in mehreren Tabellen vorkommen und über mehrere Typen verfügen. Wenn das Prädikat beim Auflösen eines solchen Spalten Typs für mehr als einen Typ gültig ist, wird die Abfrage auf Union zurückgegriffen.
Beispielsweise finden Sie [Beispiele für Fälle, in denen Find als Union fungiert](./findoperator.md#examples-of-cases-where-find-will-act-as-union).
 
## <a name="examples"></a>Beispiele

::: zone pivot="azuredataexplorer"

### <a name="term-lookup-across-all-tables-in-current-database"></a>Begriffs Suche über alle Tabellen in der aktuellen Datenbank hinweg

Die Abfrage sucht alle Zeilen aus allen Tabellen in der aktuellen Datenbank, in denen jede Spalte das Wort enthält `Kusto` .
Die resultierenden Datensätze werden entsprechend dem [Ausgabe Schema](#output-schema)transformiert.

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>Begriffs Suche über alle Tabellen hinweg, die einem Namensmuster in der aktuellen Datenbank entsprechen

Die Abfrage sucht alle Zeilen aus allen Tabellen in der aktuellen Datenbank, deren Name mit beginnt `K` und in dem eine Spalte das Wort enthält `Kusto` .
Die resultierenden Datensätze werden entsprechend dem [Ausgabe Schema](#output-schema)transformiert.

```kusto
find in (K*) where * has "Kusto"
```

### <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>Begriffs Suche über alle Tabellen in allen Datenbanken im Cluster hinweg

Die Abfrage sucht alle Zeilen aus allen Tabellen in allen Datenbanken, in denen jede Spalte das Wort enthält `Kusto` .
Bei dieser Abfrage handelt es sich um eine [datenbankübergreifende](./cross-cluster-or-database-queries.md) Abfrage.
Die resultierenden Datensätze werden entsprechend dem [Ausgabe Schema](#output-schema)transformiert.

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>Begriffs Suche über alle Tabellen und Datenbanken hinweg, die einem Namensmuster im Cluster entsprechen

Die Abfrage sucht alle Zeilen aus allen Tabellen, deren Name mit beginnt `K` , in allen Datenbanken, deren Name mit beginnt `B` und in dem eine Spalte das Wort enthält `Kusto` .
Die resultierenden Datensätze werden entsprechend dem [Ausgabe Schema](#output-schema)transformiert.

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>Begriffs Suche in mehreren Clustern

Die Abfrage sucht alle Zeilen aus allen Tabellen, deren Name mit beginnt `K` , in allen Datenbanken, deren Name mit beginnt `B` und in dem eine Spalte das Wort enthält `Kusto` .
Die resultierenden Datensätze werden entsprechend dem [Ausgabe Schema](#output-schema)transformiert.

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>Begriffs Suche über alle Tabellen hinweg

Die Abfrage sucht alle Zeilen aus allen Tabellen, in denen eine Spalte das Wort enthält `Kusto` .
Die resultierenden Datensätze werden entsprechend dem [Ausgabe Schema](#output-schema)transformiert.

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>Beispiele für `find` Ausgabe Ergebnisse  

In den folgenden Beispielen wird gezeigt, wie für `find` zwei Tabellen verwendet werden kann: *EventsTable1* und *EventsTable2*.
Angenommen, wir haben den nächsten Inhalt dieser beiden Tabellen:

### <a name="eventstable1"></a>EventsTable1

|Session_Id|Ebene|EventText|Version
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Einige text1|v 1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Einige Text2|v 1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Fehler|Einige Text3|v 1.0.1
|8f057b11-3281-45C3-A856-05ebb18a3c59|Information|Einige text4|v 1.1.0

### <a name="eventstable2"></a>EventsTable2

|Session_Id|Ebene|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Information|Andere text1|Event1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Andere Text2|Event2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Andere Text3|Event3
|15eaeab5-8576-4b58-8fc6-478f 75d8fee4|Fehler|Andere text4|Event4

### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>Suchen Sie nach allgemeinen Spalten, projizieren Sie allgemeine und ungewöhnliche Spalten, und Verpacken Sie den Rest  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|Version|EventName|pack_
|---|---|---|---|---|
|EventsTable1|Einige Text2|v 1.0.0||{"Session_Id": "acbd207d-51aa-4df7-BFA7-be70eb68f04e", "Level": "Error"}
|EventsTable2|Andere Text3||Event3|{"Session_Id": "acbd207d-51aa-4df7-BFA7-be70eb68f04e", "Level": "Error"}


### <a name="search-in-common-and-uncommon-columns"></a>In allgemeinen und ungewöhnlichen Spalten suchen

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|EventText|Version|EventName|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Einige text1|v 1.0.0
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Einige Text2|v 1.0.0
|EventsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Andere text1||Event1

Hinweis: in der Praxis werden *EventsTable1* -Zeilen mit ```Version == 'v1.0.0'``` Prädikat gefiltert, und *EventsTable2* -Zeilen werden mit ```EventName == 'Event1'``` Prädikat gefiltert.

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>Verwenden einer abgekürzten Notation zum Durchsuchen aller Tabellen in der aktuellen Datenbank

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Ebene|EventText|pack_|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Einige text1|{"Version": "v 1.0.0"}
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Einige Text2|{"Version": "v 1.0.0"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Andere Text2|{"EventName": "Event2"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Andere Text3|{"EventName": "Event3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>Die Ergebnisse der einzelnen Zeilen als Eigenschaften Behälter zurückgeben

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-BFA7-be70eb68f04e", "Level": "Information", "eventText": "Some text1", "Version": "v 1.0.0"}
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-BFA7-be70eb68f04e", "Level": "Error", "eventText": "Some Text2", "Version": "v 1.0.0"}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-BFA7-be70eb68f04e", "Level": "Information", "eventText": "Some other Text2", "EventName": "Event2"}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-BFA7-be70eb68f04e", "Level": "Error", "eventText": "Some other Text3", "EventName": "Event3"}


## <a name="examples-of-cases-where-find-will-act-as-union"></a>Beispiele für Fälle, in denen `find` fungieren soll`union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>Verwenden eines nicht tabellarischen Ausdrucks als Such Operand

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>Verweisen auf eine Spalte, die in mehreren Tabellen angezeigt wird und mehrere Typen aufweist

Angenommen, wir haben zwei Tabellen erstellt, indem wir Folgendes ausführen: 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* Die folgende Abfrage wird als ausgeführt `union` .

```kusto
find in (Table1, Table2) where ProcessId == 1001
```

Das Ausgabe Ergebnis Schema ist *(Level: String, timestamp, ProcessId_string ProcessId_int)*.

* Die folgende Abfrage wird auch als ausgeführt `union` , aber es wird ein anderes Ergebnis Schema erstellt.

```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

Das Ausgabe Ergebnis Schema lautet *(Level: String, timestamp, ProcessId_string)* .
