---
title: Gewerkschaftsoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Union-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b62c259e1abf1ff0e0e98a90ac4da5a000db5320
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765410"
---
# <a name="union-operator"></a>union-Operator

Verwendet mindestens zwei Tabellen und gibt die Zeilen aller Tabellen zurück. 

```kusto
Table1 | union Table2, Table3
```

**Syntax**

*T* `| union` [*UnionParameters*`kind=` `inner` | `outer`]`withsource=`[ ] [`,` *Spaltenname*`isfuzzy=` `true` | `false`] [ ] *Tabelle* [ *Tabelle*]...  

Alternatives Formular ohne piped-Eingang:

`union`[*UnionParameter*] [`kind=` `inner``isfuzzy=` `true` | `false` *Table* `,` *Table**ColumnName*`withsource=`] [ Spaltenname ] [ ] Tabelle [ Tabelle ]... | `outer`  

**Argumente**

::: zone pivot="azuredataexplorer"

* `Table`:
    *  Der Name einer Tabelle, `Events`z. B. ; Oder
    *  Ein Abfrageausdruck, der mit Klammern eingeschlossen werden `(Events | where id==42)` `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`muss, z. B. oder ; Oder
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise würde `E*` die Vereinigung aller Tabellen in der Datenbank `E`bilden, deren Namen beginnen.
* `kind`: 
    * `inner` : Das Ergebnis enthält die Teilmenge der Spalten, die in allen Eingabetabellen vorkommen.
    * `outer`- (Standard). Das Ergebnis enthält alle Spalten, die in einer der Eingaben auftreten. Zellen, die nicht durch eine Eingabezeile `null`definiert wurden, werden auf festgelegt.
* `withsource`=*ColumnName*: Wenn angegeben, enthält die Ausgabe eine Spalte namens *ColumnName,* deren Wert angibt, welche Quelltabelle jede Zeile beigesteuert hat.
Wenn die Abfrage effektiv (nach Platzhalterabgleich) auf Tabellen aus mehr als einer Datenbank verweist (Standarddatenbank zählt immer), hat der Wert dieser Spalte einen Tabellennamen, der mit der Datenbank qualifiziert ist.
Ebenso sind __Cluster- und Datenbankqualifikationen__ im Wert vorhanden, wenn auf mehr als ein Cluster verwiesen wird. 
* `isfuzzy=``true` `isfuzzy` : Wenn auf `true` gesetzt ist - erlaubt Fuzzy-Auflösung von Union Beinen.  |  `false` `Fuzzy`gilt für den `union` Satz von Quellen. Dies bedeutet, dass beim Analysieren der Abfrage und beim Vorbereiten der Ausführung der Satz von Union-Quellen auf den Satz von Tabellenverweisen reduziert wird, die vorhanden sind und auf die zu diesem Zeitpunkt zugegriffen werden kann. Wenn mindestens eine solche Tabelle gefunden wurde, führt ein Auflösungsfehler zu einer Warnung in den Abfragestatusergebnissen (eine für jeden fehlenden Verweis), verhindert jedoch nicht die Abfrageausführung. Wenn keine Lösungen erfolgreich waren, gibt die Abfrage einen Fehler zurück.
Der Standardwert lautet `isfuzzy=` `false`.
* *UnionParameters*: Null oder mehr (raumgetrennte) Parameter in Form von *Name* `=` *Value,* die das Verhalten des Zeilenübereinstimmungsvorgangs und des Ausführungsplans steuern. Die folgenden Parameter werden unterstützt: 

  |Name           |Werte                                        |BESCHREIBUNG                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Anzahl*|Gibt das System an, wie `union` viele gleichzeitige Unterabfragen des Operators parallel ausgeführt werden sollen. *Standard*: Anzahl der CPU-Kerne auf dem einzelnen Knoten des Clusters (2 bis 16).|
  |`hint.spread`|*Anzahl*|Gibt das System an, wie viele Knoten `union` von der gleichzeitigen Unterabfrageausführung verwendet werden sollen. *Standard :* 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  Der Name einer Tabelle, z. B.`Events`
    *  Ein Abfrageausdruck, der mit Klammern eingeschlossen werden muss, z. B.`(Events | where id==42)`
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise würde `E*` die Vereinigung aller Tabellen in der Datenbank `E`bilden, deren Namen beginnen.
* `kind`: 
    * `inner` : Das Ergebnis enthält die Teilmenge der Spalten, die in allen Eingabetabellen vorkommen.
    * `outer`- (Standard). Das Ergebnis enthält alle Spalten, die in einer der Eingaben auftreten. Zellen, die nicht durch eine Eingabezeile `null`definiert wurden, werden auf festgelegt.
* `withsource`=*ColumnName*: Wenn angegeben, enthält die Ausgabe eine Spalte namens *ColumnName,* deren Wert angibt, welche Quelltabelle jede Zeile beigesteuert hat.
Wenn die Abfrage effektiv (nach Platzhalterabgleich) auf Tabellen aus mehr als einer Datenbank verweist (Standarddatenbank zählt immer), hat der Wert dieser Spalte einen Tabellennamen, der mit der Datenbank qualifiziert ist.
Ebenso sind die __Cluster- und Datenbankqualifikationen__ im Wert vorhanden, wenn auf mehr als ein Cluster verwiesen wird. 
* `isfuzzy=``true` `isfuzzy` : Wenn auf `true` gesetzt ist - erlaubt Fuzzy-Auflösung von Union Beinen.  |  `false` `Fuzzy`gilt für den `union` Satz von Quellen. Dies bedeutet, dass beim Analysieren der Abfrage und beim Vorbereiten der Ausführung der Satz von Union-Quellen auf den Satz von Tabellenverweisen reduziert wird, die vorhanden sind und auf die zu diesem Zeitpunkt zugegriffen werden kann. Wenn mindestens eine solche Tabelle gefunden wurde, führt ein Auflösungsfehler zu einer Warnung in den Abfragestatusergebnissen (eine für jeden fehlenden Verweis), verhindert jedoch nicht die Abfrageausführung. Wenn keine Lösungen erfolgreich waren, gibt die Abfrage einen Fehler zurück.
Der Standardwert lautet `isfuzzy=false`.

::: zone-end

**Rückgabe**

Eine Tabelle mit derselben Anzahl von Zeilen, wie in allen Eingabetabellen vorhanden sind.

**Hinweise**

::: zone pivot="azuredataexplorer"

1. `union`Bereich kann [let-Anweisungen](./letstatement.md) enthalten, wenn diese mit [View-Schlüsselwort](./letstatement.md) zugeschrieben werden
2. `union`Bereich enthält keine [Funktionen](../management/functions.md). Um eine Funktion in den Union-Bereich einzuschließen, definieren Sie eine [let-Anweisung](./letstatement.md) mit [dem Schlüsselwort view](./letstatement.md)
3. Wenn `union` es sich bei der Eingabe um [Tabellen](../management/tables.md) handelt (wie gegenüber [tabellarischen Ausdrücken](./tabularexpressionstatements.md)), und auf den `union` ein where [Operator](./whereoperator.md)folgt, um eine bessere Leistung zu erzielen, sollten Sie beide durch [find](./findoperator.md)ersetzen. Beachten Sie das unterschiedliche `find` [Ausgabeschema,](./findoperator.md#output-schema) das vom Operator erstellt wird. 
4. `isfuzzy=true`gilt nur `union` für die Quellenauflösungsphase. Sobald der Satz von Quelltabellen ermittelt wurde, werden mögliche zusätzliche Abfragefehler nicht unterdrückt.
5. Bei `outer union`der Verwendung von enthält das Ergebnis alle Spalten, die in einer der Eingaben vorkommen, eine Spalte für jedes Namens- und Typvorkommen. Dies bedeutet, dass eine Spalte, die in mehreren Tabellen angezeigt wird und `union`mehrere Typen enthält, eine entsprechende Spalte für jeden Typ im Ergebnis von '. Dieser Spaltenname wird mit einem '_' gefolgt vom [Ursprungsspaltentyp](./scalar-data-types/index.md)suffixiert.

::: zone-end

::: zone pivot="azuremonitor"

1. `union`Bereich kann [let-Anweisungen](./letstatement.md) enthalten, wenn diese mit [View-Schlüsselwort](./letstatement.md) zugeschrieben werden
2. `union`Bereich enthält keine Funktionen. Um die Funktion in den Union-Bereich einzuschließen - definieren Sie eine [let-Anweisung](./letstatement.md) mit [view-Schlüsselwort](./letstatement.md)
3. Wenn `union` es sich bei der Eingabe um Tabellen `union` handelt (wie gegenüber [tabellarischen Ausdrücken](./tabularexpressionstatements.md)), und auf den ein [where-Operator](./whereoperator.md)folgt, sollten Sie beide durch [find ersetzen,](./findoperator.md) um eine bessere Leistung zu erzielen. Bitte beachten Sie das unterschiedliche `find` [Ausgabeschema](./findoperator.md#output-schema) des Bedieners. 
4. `isfuzzy=``true` gilt nur für die `union` Phase der Quellenauflösung. Sobald der Satz von Quelltabellen ermittelt wurde, werden mögliche zusätzliche Abfragefehler nicht unterdrückt.
5. Bei `outer union`der Verwendung von enthält das Ergebnis alle Spalten, die in einer der Eingaben vorkommen, eine Spalte für jedes Namens- und Typvorkommen. Dies bedeutet, dass eine Spalte, die in mehreren Tabellen angezeigt wird und `union`mehrere Typen enthält, eine entsprechende Spalte für jeden Typ im Ergebnis von '. Dieser Spaltenname wird mit einem '_' gefolgt vom [Ursprungsspaltentyp](./scalar-data-types/index.md)suffixiert.

::: zone-end


**Beispiel**

```kusto
union K* | where * has "Kusto"
```

Zeilen aus allen Tabellen in der `K`Datenbank, deren Name `Kusto`mit beginnt und in denen eine Spalte das Wort enthält.

**Beispiel**

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

**Beispiel: Verwenden`isfuzzy=true`**
 
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

Beobachtungsabfragestatus - die folgende Warnung wurde zurückgegeben:`Failed to resolve entity 'View_3'`

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

Beobachtungsabfragestatus - die folgende Warnung wurde zurückgegeben:`Failed to resolve entity 'SomeView*'`

**Beispiel: Quellspaltentypen, die nicht übereinstimmen**
 
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

Spalte `x` `View_1` aus dem Empfangen `_long`des Suffixes , und da eine Spalte mit dem Namen `x_long` bereits im Ergebnisschema vorhanden ist, wurden die Spaltennamen dedupliziert, wodurch eine neue Spalte-`x_long1`
