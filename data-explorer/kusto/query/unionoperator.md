---
title: 'union-Operator: Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der union-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3ce7c2c09e9cd5449accfabc2a7e1cc21e4ed339
ms.sourcegitcommit: d4b359e817e002fba7320132732ce6d9cee97415
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541477"
---
# <a name="union-operator"></a>union-Operator

Verwendet mindestens zwei Tabellen und gibt die Zeilen aller Tabellen zurück. 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>Syntax

*T* `| union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

Alternative Form ohne weitergeleitete Eingabe:

`union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

## <a name="arguments"></a>Argumente

::: zone pivot="azuredataexplorer"

* `Table`:
    *  Der Name einer Tabelle, z. B. `Events`, oder
    *  Ein Abfrageausdruck, der in Klammern eingeschlossen werden muss, z. B. `(Events | where id==42)` oder `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`, oder
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. `E*` würde z. B. die Vereinigungsmenge aller Tabellen in der Datenbank bilden, deren Namen mit `E` beginnen.
* `kind`: 
    * `inner` : Das Ergebnis enthält die Teilmenge der Spalten, die in allen Eingabetabellen vorkommen.
    * `outer` (Standardwert). Das Ergebnis enthält alle Spalten, die in einer der Eingaben vorkommen. Zellen, die nicht durch eine Eingabezeile definiert wurden, werden auf `null` festgelegt.
* `withsource`=*ColumnName*: Wenn diese Einstellung festgelegt ist, enthält die Ausgabe eine Spalte namens *ColumnName*, deren Wert angibt, aus welchen Quelltabellen die einzelnen Zeilen stammen.
Wenn die Abfrage (nach Platzhalterabgleich) auf Tabellen aus mehreren Datenbanken verweist (Standarddatenbank zählt immer mit), wird für den Wert dieser Spalte ein Tabellenname mit der Datenbank qualifiziert.
Auf ähnliche Weise sind __Cluster- und Datenbankqualifikationen__ im Wert vorhanden, wenn auf mehrere Cluster verwiesen wird. 
* `isfuzzy=` `true` | `false`: Wenn `isfuzzy` auf `true` festgelegt ist, wird Fuzzyauflösung von Union-Elementen ermöglicht. `Fuzzy` gilt für den Satz von `union`-Quellen. Dies bedeutet, dass bei der Analyse der Abfrage und der Vorbereitung für die Ausführung der Satz von Union-Quellen auf den Satz von Tabellenverweisen reduziert wird, der vorhanden und jederzeit zugänglich ist. Wenn mindestens eine solche Tabelle gefunden wurde, ergibt jeder Auflösungsfehler eine Warnung in den Ergebnissen des Abfragestatus (eine Warnung für jeden fehlenden Verweis), verhindert jedoch nicht die Abfrageausführung. Wenn keine Auflösungen erfolgreich waren, gibt die Abfrage einen Fehler zurück.
Der Standardwert ist `isfuzzy=` `false`.
* *UnionParameters*: Null oder mehr (durch Leerzeichen getrennte) Parameter in Form von *Name* `=` *Wert*, die das Verhalten des Vorgangs zum Zuordnen der Zeilen und den Ausführungsplan steuern. Die folgenden Parameter werden unterstützt: 

  |Name           |Werte                                        |BESCHREIBUNG                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Number*|Weist das System darauf hin, wie viele gleichzeitige Unterabfragen des `union`-Operators parallel ausgeführt werden sollen. *Standard:* Menge der CPU-Kerne auf dem einzelnen Knoten des Clusters (2 bis 16).|
  |`hint.spread`|*Number*|Weist das System darauf hin, wie viele Knoten von der gleichzeitigen Ausführung von `union`-Unterabfragen verwendet werden sollen. *Standard:* 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  Der Name einer Tabelle, z. B. `Events`.
    *  Ein Abfrageausdruck, der in Klammern eingeschlossen werden muss, z. B. `(Events | where id==42)`.
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. `E*` würde z. B. die Vereinigungsmenge aller Tabellen in der Datenbank bilden, deren Namen mit `E` beginnen.

> [!NOTE]
> Wenn die Liste der Tabellen bekannt ist, verwenden Sie keine Platzhalter. Einige Arbeitsbereiche enthalten eine sehr große Anzahl von Tabellen, die zu einer ineffizienten Ausführung führen würden. Tabellen können ebenfalls im Laufe der Zeit hinzugefügt werden, was zu unvorhergesehenen Ergebnissen führt.
    
* `kind`: 
    * `inner` : Das Ergebnis enthält die Teilmenge der Spalten, die in allen Eingabetabellen vorkommen.
    * `outer` (Standardwert). Das Ergebnis enthält alle Spalten, die in einer der Eingaben vorkommen. Zellen, die nicht durch eine Eingabezeile definiert wurden, werden auf `null` festgelegt.
* `withsource`=*ColumnName*: Wenn diese Einstellung festgelegt ist, enthält die Ausgabe eine Spalte namens *ColumnName*, deren Wert angibt, aus welchen Quelltabellen die einzelnen Zeilen stammen.
Wenn die Abfrage (nach Platzhalterabgleich) auf Tabellen aus mehreren Datenbanken verweist (Standarddatenbank zählt immer mit), wird für den Wert dieser Spalte ein Tabellenname mit der Datenbank qualifiziert.
Auf ähnliche Weise sind die __Cluster- und Datenbankqualifikationen__ im Wert vorhanden, wenn auf mehrere Cluster verwiesen wird. 
* `isfuzzy=` `true` | `false`: Wenn `isfuzzy` auf `true` festgelegt ist, wird Fuzzyauflösung von Union-Elementen ermöglicht. `Fuzzy` gilt für den Satz von `union`-Quellen. Dies bedeutet, dass bei der Analyse der Abfrage und der Vorbereitung für die Ausführung der Satz von Union-Quellen auf den Satz von Tabellenverweisen reduziert wird, der vorhanden und jederzeit zugänglich ist. Wenn mindestens eine solche Tabelle gefunden wurde, ergibt jeder Auflösungsfehler eine Warnung in den Ergebnissen des Abfragestatus (eine Warnung für jeden fehlenden Verweis), verhindert jedoch nicht die Abfrageausführung. Wenn keine Auflösungen erfolgreich waren, gibt die Abfrage einen Fehler zurück.
Der Standardwert ist `isfuzzy=false`.

::: zone-end

## <a name="returns"></a>Gibt zurück

Eine Tabelle mit derselben Anzahl von Zeilen, wie in allen Eingabetabellen vorhanden sind.

**Hinweise**

::: zone pivot="azuredataexplorer"

1. Der `union`-Bereich kann [let-Anweisungen](./letstatement.md) enthalten, wenn diese mit dem [View-Schlüsselwort](./letstatement.md) attributiert werden.
2. Der `union`-Bereich enthält keine [Funktionen](../management/functions.md). Wenn Sie eine Funktion in den Union-Bereich einschließen möchten, definieren Sie eine [let-Anweisung](./letstatement.md) mit dem [View-Schlüsselwort](./letstatement.md).
3. Wenn die `union`-Eingabe [Tabellen](../management/tables.md) (im Gegensatz zu [tabellarischen Ausdrücken](./tabularexpressionstatements.md)) sind und auf `union` ein [where-Operator](./whereoperator.md) folgt, um bessere Leistung zu erzielen, empfiehlt es sich, beides durch [find](./findoperator.md) zu ersetzen. Beachten Sie die verschiedenen [Ausgabeschemas](./findoperator.md#output-schema), die vom `find`-Operator generiert werden. 
4. `isfuzzy=true` gilt nur für die Auflösungsphase der `union`-Quellen. Sobald der Satz von Quelltabellen bestimmt wird, werden mögliche zusätzliche Abfragefehler nicht unterdrückt.
5. Wenn Sie `outer union` verwenden, enthält das Ergebnis alle Spalten, die in einer der Eingaben vorkommen: eine Spalte für jeden Namen und jeden Typ von Vorkommen. Dies bedeutet Folgendes: Wenn eine Spalte in mehreren Tabellen vorkommt und mehrere Typen aufweist, enthält sie eine entsprechende Spalte für jeden Typ im Ergebnis der `union`. Dieser Spaltenname wird mit einem „_“ als Suffix versehen, gefolgt vom ursprünglichen [Typ](./scalar-data-types/index.md) der Spalte.

::: zone-end

::: zone pivot="azuremonitor"

1. Der `union`-Bereich kann [let-Anweisungen](./letstatement.md) enthalten, wenn diese mit dem [View-Schlüsselwort](./letstatement.md) attributiert werden.
2. Der `union`-Bereich enthält keine Funktionen. Wenn Sie eine Funktion in den Union-Bereich einschließen möchten, definieren Sie eine [let-Anweisung](./letstatement.md) mit dem [View-Schlüsselwort](./letstatement.md).
3. Wenn die `union`-Eingabe Tabellen (im Gegensatz zu [tabellarischen Ausdrücken](./tabularexpressionstatements.md)) sind und auf `union` ein [where-Operator](./whereoperator.md) folgt, empfiehlt es sich, beides durch [find](./findoperator.md) zu ersetzen, um bessere Leistung zu erzielen. Beachten Sie die verschiedenen [Ausgabeschemas](./findoperator.md#output-schema), die vom `find`-Operator generiert werden. 
4. `isfuzzy=` `true` gilt nur für die Phase der Auflösung von `union`-Quellen. Sobald der Satz von Quelltabellen bestimmt wurde, werden mögliche zusätzliche Abfragefehler nicht unterdrückt.
5. Wenn Sie `outer union` verwenden, enthält das Ergebnis alle Spalten, die in einer der Eingaben vorkommen: eine Spalte für jeden Namen und jeden Typ von Vorkommen. Dies bedeutet Folgendes: Wenn eine Spalte in mehreren Tabellen vorkommt und mehrere Typen aufweist, enthält sie eine entsprechende Spalte für jeden Typ im Ergebnis der `union`. Dieser Spaltenname wird mit einem „_“ als Suffix versehen, gefolgt vom ursprünglichen [Typ](./scalar-data-types/index.md) der Spalte.

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>Beispiel: Tabellen mit einer Zeichenfolge im Namen oder einer Spalte

```kusto
union K* | where * has "Kusto"
```

Zeilen aus allen Tabellen in der Datenbank, deren Name mit `K` beginnt und in denen eine beliebige Spalten das Wort `Kusto` enthält.

## <a name="example-distinct-count"></a>Beispiel: Distinct Count

```kusto
union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```

Die Anzahl der unterschiedlichen Benutzer, die während des Vortags entweder ein `Query`- oder ein `Command`-Ereignis herbeigeführt haben. Im Ergebnis gibt die Spalte „SourceTable“ entweder „Abfrage“ oder „Befehl“ an.

```kusto
Query
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Mit dieser effizienteren Version wird dasselbe Ergebnis erzielt. Jede Tabelle wird vor dem Erstellen der Union gefiltert.

**Beispiel: Verwenden von `isfuzzy=true`**
 
```kusto     
// Using union isfuzzy=true to access non-existing view:                                     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true
(View_1 | where x > 0), 
(View_2 | where x > 0),
(View_3 | where x > 0)
| count 
```

|Anzahl|
|---|
|2|

Der Abfragestatus wird beobachtet: Die folgende Warnung wurde zurückgegeben: `Failed to resolve entity 'View_3'`

```kusto
// Using union isfuzzy=true and wildcard access:
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|Anzahl|
|---|
|3|

Der Abfragestatus wird beobachtet: Die folgende Warnung wurde zurückgegeben: `Failed to resolve entity 'SomeView*'`

**Beispiel: Quellspaltentypen stimmen nicht überein**
 
```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
union withsource=TableName View_1, View_2
```

|TableName|x_long|x_int|
|---------|------|-----|
|View_1   |1     |     |
|View_2   |      |2    |

```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
let View_3 = view () { print x_long=3 };
union withsource=TableName View_1, View_2, View_3 
```

|TableName|x_long1|x_int |x_long|
|---------|-------|------|------|
|View_1   |1      |      |      |
|View_2   |       |2     |      |
|View_3   |       |      |3     |

Die Spalte `x` aus `View_1` hat das Suffix `_long` erhalten, und da eine Spalte mit dem Namen `x_long` bereits im Ergebnisschema vorhanden ist, wurden die Spaltennamen dedupliziert, sodass eine neue Spalte erstellt wurde: `x_long1`.
