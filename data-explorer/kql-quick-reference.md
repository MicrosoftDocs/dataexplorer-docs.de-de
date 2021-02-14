---
title: KQL-Kurzübersicht
description: Eine Liste mit hilfreichen KQL-Funktionen und deren Definitionen mit Syntaxbeispielen.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 2fef26a22388d092cd5b8baa7ebca11ae7da4d05
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359708"
---
# <a name="kql-quick-reference"></a>KQL-Kurzübersicht

Dieser Artikel enthält eine Liste mit Funktionen und Beschreibungen, um Sie bei Ihren ersten Schritten mit der Kusto-Abfragesprache zu unterstützen.

| Operator/Funktion                               | BESCHREIBUNG                           | Syntax                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filter/Suche/Bedingung**                      |**_Suche nach relevanten Daten mittels Filter- oder Suchvorgang_** |                      |
| [where](kusto/query/whereoperator.md)                      | Filtert nach einem bestimmten Prädikat.           | `T | where Predicate`                         |
| [where contains/has](kusto/query/whereoperator.md)        | `Contains`: Sucht nach einer beliebigen Teilzeichenfolgenübereinstimmung. <br> `Has`: Sucht nach einem bestimmten Wort (bessere Leistung).  | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | Durchsucht alle Spalten in der Tabelle nach dem Wert. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | Gibt die angegebene Anzahl von Datensätzen zurück. Verwenden Sie diese Option, um eine Abfrage zu testen.<br>**_Hinweis_** : `_take`_ und `_limit`_ sind Synonyme. | `T | take NumberOfRows` |
| [case](kusto/query/casefunction.md)                        | Fügt eine Bedingungsanweisung hinzu (vergleichbar mit „if“/„then“/„elseif“ in anderen Systemen). | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | Erzeugt eine Tabelle mit der speziellen Kombination der bereitgestellten Spalten der Eingabetabelle. | `distinct [ColumnName], [ColumnName]` |
| **Datum/Uhrzeit**                                   |**_Vorgänge mit Datums- und Uhrzeitfunktionen_**               |                          |
|[ago](kusto/query/agofunction.md)                           | Gibt den Zeitversatz relativ zum Zeitpunkt der Abfrageausführung zurück. `ago(1h)` bedeutet beispielsweise „eine Stunde vor dem aktuellen Uhrzeitwert“. | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | Gibt Daten in [verschiedenen Datumsformaten](kusto/query/format-datetimefunction.md#supported-formats) zurück. | `format_datetime(datetime , format)` |
| [bin](kusto/query/binfunction.md)                          | Rundet alle Werte in einem Zeitrahmen und gruppiert sie. | `bin(value,roundTo)` |
| **Erstellen/Entfernen von Spalten**                   |**_Hinzufügen oder Entfernen von Spalten in einer Tabelle_** |                                                    |
| [print](kusto/query/printoperator.md)                      | Gibt eine einzelne Zeile mit mindestens einem skalaren Ausdruck aus. | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | Wählt die einzuschließenden Spalten in der angegebenen Reihenfolge aus. | `T | project ColumnName [= Expression] [, ...]` <br> oder <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | Wählt die Spalten aus, die von der Ausgabe ausgeschlossen werden sollen. | `T | project-away ColumnNameOrPattern [, ...]` |
| [project-keep](kusto/query/project-keep-operator.md)         | Wählt die Spalten aus, die in die Ausgabe eingeschlossen werden sollen. | `T | project-keep ColumnNameOrPattern [, ...]` |
| [project-rename](kusto/query/projectrenameoperator.md)     | Benennt Spalten in der Ergebnisausgabe um. | `T | project-rename new_column_name = column_name` |
| [project-reorder](kusto/query/projectreorderoperator.md)   | Ordnet Spalten in der Ergebnisausgabe neu an. | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | Erstellt eine berechnete Spalte und fügt sie dem Resultset hinzu. | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Sortieren und Aggregieren des Datasets**                 |**_Ändern der Datenstruktur durch sinnvolles Sortieren oder Gruppieren_**|                  |
| [sort](kusto/query/sortoperator.md)                        | Sortiert die Zeilen der Eingabetabelle nach mindestens einer Spalte in aufsteigender oder absteigender Reihenfolge. | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](kusto/query/topoperator.md)                          | Gibt die ersten n Zeilen des Datasets zurück, wenn das Dataset mithilfe von `by` sortiert wird. | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | Gruppiert die Zeilen gemäß den `by`-Gruppenspalten und berechnet Aggregationen für jede Gruppe. | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | Zählt Datensätze in der Eingabetabelle (beispielsweise „T“).<br>Dieser Operator ist eine Kurzform von `summarize count() `.| `T | count` |
| [join](kusto/query/joinoperator.md)                        | Führt die Zeilen zweier Tabellen zusammen, um eine neue Tabelle zu erzeugen, indem Werte aus den angegebenen Spalten beider Tabellen abgeglichen werden. Unterstützt eine breite Palette von Join-Typen: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | Akzeptiert zwei oder mehr Tabellen und gibt alle ihre Zeilen zurück. | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | Generiert eine Tabelle mit einer arithmetischen Reihe von Werten. | `range columnName from start to stop step step` |
| **Formatieren von Daten**                                 | **_Ändern der Datenstruktur für eine aussagekräftige Ausgabe_** | |
| [lookup](kusto/query/lookupoperator.md)                    | Erweitert die Spalten einer Faktentabelle mit nachgeschlagenen Werten aus einer Dimensionstabelle. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | Konvertiert dynamische Arrays in Zeilen (mehrwertige Erweiterung). | `T | mv-expand Column` |
| [parse](kusto/query/parseoperator.md)                      | Wertet einen Zeichenfolgenausdruck aus und analysiert dessen Wert in eine oder mehrere berechnete Spalten. Ermöglicht die Strukturierung unstrukturierter Daten. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | Erstellt eine Reihe angegebener aggregierter Werte entlang einer angegebenen Achse. | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](kusto/query/letstatement.md)                         | Bindet einen Namen an Ausdrücke, die auf den gebundenen Wert verweisen können. Bei den Werten kann es sich um Lambdaausdrücke handeln, um Ad-hoc-Funktionen als Teil der Abfrage zu erstellen. Verwenden Sie `let`, um Ausdrücke für Tabellen zu erstellen, deren Ergebnisse wie eine neue Tabelle aussehen. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Allgemein**                                     | **_Sonstige Vorgänge und Funktionen_** | |
| [invoke](kusto/query/invokeoperator.md)                    | Führt die Funktion für die Tabelle aus, die sie als Eingabe erhält. | `T | invoke function([param1, param2])` |
| [evaluate pluginName](kusto/query/evaluateoperator.md)     | Überprüft Erweiterungen für die Abfragesprache (Plug-Ins). | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Visualisierung**                               | **_Vorgänge zum Anzeigen der Daten in einem grafischen Format_** | |
| [render](kusto/query/renderoperator.md) | Rendert Ergebnisse als grafische Ausgabe. | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
