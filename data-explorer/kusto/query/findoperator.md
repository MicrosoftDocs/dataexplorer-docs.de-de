---
title: Suchoperator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Suchoperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4c3db23128c47c86639f15286cbcbcb748157386
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765921"
---
# <a name="find-operator"></a>find-Operator

Sucht Zeilen, die einem Prädikat über eine Gruppe von Tabellen hinweg entsprechen.

::: zone pivot="azuredataexplorer"

Der Bereich `find` des kann auch datenbank- oder clusterübergreifend sein.

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

* `find`[`withsource`=*Spaltenname*] [`in` `(` *Table* Tabelle`,` [ *Tabelle*, ...] `)`] `where` *Prädikat* `project-smart`  |  `project` [`:` *ColumnName* [`,` *ColumnType*] [ *ColumnName*[`:`*ColumnType*], ...] [`,` `pack(*)`]] 

* `find`*Prädikat* `project-smart`  |  `project` [ *ColumnName*`:`[`,` *ColumnType*] [ *ColumnName*[`:`*ColumnType*], ...] [`, pack(*)`]] 

## <a name="arguments"></a>Argumente

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: Optional. Standardmäßig enthält die Ausgabe eine Spalte namens *source_* deren Werte angibt, welche Quelltabelle jede Zeile beigesteuert hat. Wenn angegeben, wird *ColumnName* anstelle von *source_* verwendet.
Wenn die Abfrage effektiv (nach Platzhalterabgleich) auf Tabellen aus mehr als einer Datenbank verweist (Standarddatenbank zählt immer), hat der Wert dieser Spalte einen Tabellennamen, der mit der Datenbank qualifiziert ist. Ebenso sind __Cluster- und Datenbankqualifikationen__ im Wert vorhanden, wenn auf mehr als ein Cluster verwiesen wird.
* *Prädikat*: [siehe Details](./findoperator.md#predicate-syntax). Ein `boolean` [Ausdruck](./scalar-data-types/bool.md) über den Spalten der Eingabetabellen *Tabelle* [`,` *Tabelle*, ...]. Sie wird für jede Zeile in jeder Eingabetabelle ausgewertet. 
* `Table`: Optional. Standardmäßig wird *der Sucher* in allen Tabellen in der aktuellen Datenbank gesucht.
    *  Der Name einer Tabelle, `Events` z. B. oder
    *  Ein Abfrageausdruck, z.B. `(Events | where id==42)`.
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise würde `E*` die Vereinigung aller Tabellen in der Datenbank `E`bilden, deren Namen beginnen.
* `project-smart` | `project`: [Siehe Details,](./findoperator.md#output-schema) falls nicht angegeben, `project-smart` wird standardmäßig verwendet

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: Optional. Standardmäßig enthält die Ausgabe eine Spalte namens *source_* deren Werte angibt, welche Quelltabelle jede Zeile beigesteuert hat. Wenn angegeben, wird *ColumnName* anstelle von *source_* verwendet.
* *Prädikat*: [siehe Details](./findoperator.md#predicate-syntax). Ein `boolean` [Ausdruck](./scalar-data-types/bool.md) über den Spalten der Eingabetabellen *Tabelle* [`,` *Tabelle*, ...]. Sie wird für jede Zeile in jeder Eingabetabelle ausgewertet. 
* `Table`: Optional. Standardmäßig sucht *der Sucher* in allen Tabellen
    *  Der Name einer Tabelle, `Events` z. B. oder
    *  Ein Abfrageausdruck, z.B. `(Events | where id==42)`.
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise würde `E*` die Vereinigung aller Tabellen bilden, `E`deren Namen beginnen.
* `project-smart` | `project`: [Siehe Details,](./findoperator.md#output-schema) falls nicht angegeben, `project-smart` wird standardmäßig verwendet

::: zone-end

## <a name="returns"></a>Rückgabe

Transformation von *Table* Zeilen`,` in Tabelle [ *Tabelle*, `true`...], für die *Prädikat* ist . Die Zeilen werden entsprechend dem Ausgabeschema transformiert, wie unten beschrieben.

## <a name="output-schema"></a>Ausgabeschema

**source_ Spalte**

Die Ausgabe des find-Operators enthält immer eine *source_* Spalte mit dem Quelltabellennamen. Die Spalte kann mit `withsource` dem Parameter umbenannt werden.

**Ergebnisspalten**

Quelltabellen, die keine Spalte enthalten, die von der Prädikatauswertung verwendet wird, werden herausgefiltert.

Bei `project-smart`Verwendung von werden die Spalten, die in der Ausgabe angezeigt werden, wie:
1. Spalten, die explizit im Prädikat angezeigt werden
2. Spalten, die allen gefilterten Tabellen gemeinsam sind Der Rest der Spalten wird in `pack_` einen Eigenschaftenbeutel gepackt und in einer zusätzlichen Spalte angezeigt.
Eine Spalte, auf die explizit vom Prädikat verwiesen wird und in mehreren Tabellen mit mehreren Typen angezeigt wird, hat für jeden solchen Typ eine andere Spalte im Ergebnisschema. Jeder der Spaltennamen wird aus dem ursprünglichen Spaltennamen und dem Typ erstellt, getrennt durch einen Unterstrich.

Bei `project` Verwendung von`:` *ColumnName*`,` [*ColumnType*] [ *ColumnName*[`:`*ColumnType*], ...] [`,` `pack(*)`]:
1. Die Ergebnistabelle enthält die in der Liste angegebenen Spalten. Wenn eine Quelltabelle keine bestimmte Spalte enthält, sind die Werte in den entsprechenden Zeilen null.
2. Wenn Sie einen *ColumnType* zusammen mit einem *ColumnName*angeben, hat diese Spalte im **Ergebnis** den angegebenen Typ, und die Werte werden bei Bedarf in diesen Typ umgegeut. Beachten Sie, dass sich dies beim Auswerten des *Prädikats*nicht auf den Spaltentyp auswirkt.
3. Wenn `pack(*)` die Verwendung verwendet wird, werden die restlichen Spalten in eine `pack_` Sachtasche gepackt und in einer zusätzlichen Spalte angezeigt.

**pack_ Spalte**

Diese Spalte enthält einen Eigenschaftenbeutel mit den Daten aus allen Spalten, die nicht im Ausgabeschema angezeigt werden. Der Name der Quellspalte dient als Eigenschaftsname und der Spaltenwert als Eigenschaftswert.

## <a name="predicate-syntax"></a>Prädikatsyntax

Eine Zusammenfassung der Filterfunktionen finden Sie im [Where-Operator.](./whereoperator.md)

Find-Operator unterstützt eine `* has` alternative Syntax für *Begriff* , und die Verwendung von nur *Begriff* durchsucht einen Begriff in allen Eingabespalten

## <a name="notes"></a>Notizen

* Wenn `project` die Klausel auf eine Spalte verweist, die in mehreren Tabellen angezeigt wird und mehrere Typen enthält, muss ein Typ diesem Spaltenverweis in der Projektklausel folgen.
* Wenn eine Spalte in mehreren Tabellen `project-smart` angezeigt wird und mehrere Typen enthält und verwendet `find`wird, gibt es für jeden Typ im Ergebnis von ',' eine entsprechende Spalte, wie in [Union](./unionoperator.md) beschrieben.
* Bei Verwendung von *project-smart*können Änderungen im Prädikat, in den Quelltabellen oder im Tabellenschema zu Änderungen am Ausgabeschema führen. Wenn ein konstantes Ergebnisschema erforderlich ist, verwenden Sie stattdessen *Project*
* `find`Bereich kann keine [Funktionen](../management/functions.md)enthalten . Um eine Funktion in den Suchbereich einzuschließen - definieren Sie eine [let-Anweisung](./letstatement.md) mit [view-Schlüsselwort](./letstatement.md)

## <a name="performance-tips"></a>Tipps zur Leistungssteigerung

* [Tabellen](../management/tables.md) im Gegensatz zu [tabellenförmigen Ausdrücken](./tabularexpressionstatements.md)verwenden - im Falle `union` eines tabellarischen Ausdrucks greift der Suchoperator auf eine Abfrage zurück, was zu einer Leistungseinbußen führen kann.
* Wenn eine Spalte, die in mehreren Tabellen angezeigt wird und mehrere Typen enthält, Teil der Projektklausel ist, `find` ziehen Sie das Hinzufügen eines *ColumnType* zur Projektklausel vor, anstatt die Tabelle vor dem Übergeben zu ändern (siehe vorherige Spitze)
* Hinzufügen zeitbasierter Filter zum Prädikat (mit einem Datetime-Spaltenwert oder [ingestion_time()](./ingestiontimefunction.md))
* Bevorzugen Sie die Suche in bestimmten Spalten über die Volltextsuche 
* Es ist vorzuziehen, explizit auf Spalten zu verweisen, die in mehreren Tabellen angezeigt werden und mehrere Typen aufweisen. Wenn das Prädikat gültig ist, wenn solche Spaltentypen für mehr als einen Typ aufgelöst werden, tritt die Abfrage auf Union zurück.

[siehe Beispiele](./findoperator.md#examples-of-cases-where-find-will-perform-as-union)

## <a name="examples"></a>Beispiele

::: zone pivot="azuredataexplorer"

###  <a name="term-lookup-across-all-tables-in-current-database"></a>Begriffssuche in allen Tabellen (in der aktuellen Datenbank)

Die nächste Abfrage sucht alle Zeilen aus allen Tabellen in `Kusto`der aktuellen Datenbank, in der eine Spalte das Wort enthält. Die resultierenden Datensätze werden entsprechend dem [Ausgabeschema](#output-schema)transformiert. 

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>Begriffssuche für alle Tabellen, die einem Namensmuster entsprechen (in der aktuellen Datenbank)

Die nächste Abfrage sucht alle Zeilen aus allen Tabellen `K`in der aktuellen Datenbank, `Kusto`deren Name mit beginnt und in denen eine Spalte das Wort enthält.
Die resultierenden Datensätze werden entsprechend dem [Ausgabeschema](#output-schema)transformiert. 

```kusto
find in (K*) where * has "Kusto"
```

###  <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>Begriffssuche für alle Tabellen in allen Datenbanken (im Cluster)

Die nächste Abfrage sucht alle Zeilen aus allen Tabellen in `Kusto`allen Datenbanken, in denen eine Spalte das Wort enthält. Dies ist eine [datenbankübergreifende Abfrageabfrage.](./cross-cluster-or-database-queries.md) Die resultierenden Datensätze werden entsprechend dem [Ausgabeschema](#output-schema)transformiert. 

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>Begriffssuche in allen Tabellen und Datenbanken, die einem Namensmuster entsprechen (im Cluster)

Die nächste Abfrage sucht alle Zeilen aus `K` allen Tabellen, deren `B` Name mit in allen `Kusto`Datenbanken beginnt, deren Name mit beginnt und in denen eine Spalte das Wort enthält. Die resultierenden Datensätze werden entsprechend dem [Ausgabeschema](#output-schema)transformiert. 

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>Terminologiesuche in mehreren Clustern

Die nächste Abfrage sucht alle Zeilen aus `K` allen Tabellen, deren `B` Name mit in allen `Kusto`Datenbanken beginnt, deren Name mit beginnt und in denen eine Spalte das Wort enthält. Die resultierenden Datensätze werden entsprechend dem [Ausgabeschema](#output-schema)transformiert. 

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>Begriffssuche für alle Tabellen

Die nächste Abfrage sucht alle Zeilen aus allen `Kusto`Tabellen, in denen eine Spalte das Wort enthält. Die resultierenden Datensätze werden entsprechend dem [Ausgabeschema](#output-schema)transformiert. 

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>Beispiele `find` für Ausgabeergebnisse  

Die folgenden Beispiele `find` zeigen, wie sie über zwei Tabellen verwendet werden können: EventsTable1 und EventsTable2. Angenommen, wir haben den nächsten Inhalt dieser beiden Tabellen: 

### <a name="eventstable1"></a>EventsTabelle1

|Session_id|Ebene|EventText|Version
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Einige Text1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Einige Text2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Fehler|Einige Text3|v1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|Information|Einige Text4|v1.1.0

### <a name="eventstable2"></a>EventsTabelle2

|Session_id|Ebene|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Information|Einige andere Texte1|Event1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Einige andere Texte2|Event2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Some Other Text3|Event3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|Fehler|Einige andere Texte4|Event4


### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>Suchen Sie in gemeinsamen Spalten, Projekt gemeinsamen und ungewöhnlichen Spalten und packen Sie den Rest  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|Version|EventName|pack_
|---|---|---|---|---|
|EventsTabelle1|Einige Text2|v1.0.0||"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error"
|EventsTabelle2|Some Other Text3||Event3|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error"


### <a name="search-in-common-and-uncommon-columns"></a>Suchen in allgemeinen und ungewöhnlichen Spalten

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_id|EventText|Version|EventName|
|---|---|---|---|---|
|EventsTabelle1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Einige Text1|v1.0.0
|EventsTabelle1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Einige Text2|v1.0.0
|EventsTabelle2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Einige andere Texte1||Event1

Hinweis: In der Praxis werden *EventsTable1-Zeilen* mit ```Version == 'v1.0.0'``` Prädikatgefiltert und *EventsTable2-Zeilen* mit ```EventName == 'Event1'``` Prädikat gefiltert.

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>Verwenden Sie die abgekürzte Notation, um alle Tabellen in der aktuellen Datenbank zu durchsuchen

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_id|Ebene|EventText|pack_|
|---|---|---|---|---|
|EventsTabelle1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Einige Text1|"Version":"v1.0.0"
|EventsTabelle1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Einige Text2|"Version":"v1.0.0"
|EventsTabelle2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|Einige andere Texte2|"EventName":"Ereignis2"
|EventsTabelle2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Fehler|Some Other Text3|"EventName":"Ereignis3"


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>Geben Sie die Ergebnisse aus jeder Zeile als Eigenschaftentasche zurück

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTabelle1|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Information", "EventText":"Some Text1", "Version":"v1.0.0"
|EventsTabelle1|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error", "EventText":"Some Text2", "Version":"v1.0.0"
|EventsTabelle2|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Information", "EventText":"Some Other Text2", "EventName":"Event2"
|EventsTabelle2|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level":"Error", "EventText":"Some Other Text3", "EventName":"Event3"


## <a name="examples-of-cases-where-find-will-perform-as-union"></a>Beispiele für `find` Fälle, in denen`union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>Verwenden eines nicht tabellarischen Ausdrucks als Finden von Operanden

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>Verweisen auf eine Spalte, die in mehreren Tabellen angezeigt wird und mehrere Typen enthält

Angenommen, wir haben zwei Tabellen erstellt, indem Sie: 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* Die folgende Abfrage wird `union`wie folgt ausgeführt:
```kusto
find in (Table1, Table2) where ProcessId == 1001
```

Und das Ausgabeergebnisschema wird __sein (Level:string, Timestamp, ProcessId_string, ProcessId_int)__

* Die folgende Abfrage wird ebenfalls ausgeführt, `union` erzeugt aber ein anderes Ergebnisschema:
```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

Und das Ausgabeergebnisschema wird __sein (Level:string, Timestamp, ProcessId_string)__
