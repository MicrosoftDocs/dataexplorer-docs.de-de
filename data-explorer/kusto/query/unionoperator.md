---
title: Union-Operator-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt den Union-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4995c989b04df5dd66e20e3405b5fb7da7c739ce
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954704"
---
# <a name="union-operator"></a>union-Operator

Verwendet mindestens zwei Tabellen und gibt die Zeilen aller Tabellen zurück. 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>Syntax

*T* `| union` [*unionparameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ]- *Tabelle* [ `,` *Tabelle*]...  

Alternatives Formular ohne weitergeleitete Eingabe:

`union`[*Unionparameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ]- *Tabelle* [ `,` *Tabelle*]...  

## <a name="arguments"></a>Argumente

::: zone pivot="azuredataexplorer"

* `Table`:
    *  Der Name einer Tabelle, z. b `Events` . oder.
    *  Ein Abfrage Ausdruck, der in Klammern eingeschlossen werden muss, z `(Events | where id==42)` . b `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))` . oder.
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise `E*` würde die Vereinigung aller Tabellen in der Datenbank bilden, deren Namen beginnen `E` .
* `kind`: 
    * `inner` : Das Ergebnis enthält die Teilmenge der Spalten, die in allen Eingabetabellen vorkommen.
    * `outer` -(Standard). Das Ergebnis enthält alle Spalten, die in einer der Eingaben auftreten. Zellen, die nicht durch eine Eingabezeile definiert wurden, werden auf festgelegt `null` .
* `withsource`=*ColumnName*: Wenn angegeben, enthält die Ausgabe eine Spalte namens *ColumnName* , deren Wert angibt, welche Quell Tabelle die einzelnen Zeilen beigetragen hat.
Wenn die Abfrage (nach Platzhalter Übereinstimmung) auf Tabellen aus mehreren Datenbanken verweist (Standarddatenbank immer zählt), wird für den Wert dieser Spalte ein Tabellenname mit der Datenbank qualifiziert.
Entsprechend werden __Cluster-und Daten Bank__ Qualifizierungen im Wert vorhanden sein, wenn auf mehr als ein Cluster verwiesen wird. 
* `isfuzzy=``true`  |  `false` : Wenn `isfuzzy` auf festgelegt ist, `true` lässt die fuzzyauflösung von Union-Beinen zu. `Fuzzy` gilt für den Satz von `union` Quellen. Dies bedeutet, dass bei der Analyse der Abfrage und der Vorbereitung auf die Ausführung der Satz von Union-Quellen auf den Satz von Tabellen verweisen reduziert wird, der vorhanden und jederzeit zugänglich ist. Wenn mindestens eine solche Tabelle gefunden wurde, ergibt jeder Auflösungs Fehler eine Warnung in den Ergebnissen des Abfrage Status (eine für jeden fehlenden Verweis), verhindert jedoch die Abfrage Ausführung. Wenn keine Auflösungen erfolgreich waren, gibt die Abfrage einen Fehler zurück.
Der Standardwert lautet `isfuzzy=` `false`.
* *Unionparameters*: NULL oder mehr (durch Leerzeichen getrennte) Parameter in der Form des *namens* `=` *Werts* , die das Verhalten der Zeilen Übereinstimmungs Operation und des Ausführungs Plans steuern. Die folgenden Parameter werden unterstützt: 

  |Name           |Werte                                        |BESCHREIBUNG                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Zahl*|Gibt an, wie viele gleichzeitige Unterabfragen des `union` Operators parallel ausgeführt werden sollen. *Standard*Wert: CPU-Kerne auf dem einzelnen Knoten des Clusters (2 bis 16).|
  |`hint.spread`|*Zahl*|Gibt an, wie viele Knoten von der gleichzeitigen Ausführung von `union` Unterabfragen verwendet werden sollen. *Standard*Wert: 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  Der Name einer Tabelle, z. b. `Events`
    *  Ein Abfrage Ausdruck, der in Klammern eingeschlossen werden muss, z. b. `(Events | where id==42)`
    *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. Beispielsweise `E*` würde die Vereinigung aller Tabellen in der Datenbank bilden, deren Namen beginnen `E` .
* `kind`: 
    * `inner` : Das Ergebnis enthält die Teilmenge der Spalten, die in allen Eingabetabellen vorkommen.
    * `outer` -(Standard). Das Ergebnis enthält alle Spalten, die in einer der Eingaben auftreten. Zellen, die nicht durch eine Eingabezeile definiert wurden, werden auf festgelegt `null` .
* `withsource`=*ColumnName*: Wenn angegeben, enthält die Ausgabe eine Spalte namens *ColumnName* , deren Wert angibt, welche Quell Tabelle für die einzelnen Zeilen beigetragen hat.
Wenn die Abfrage (nach Platzhalter Übereinstimmung) auf Tabellen aus mehreren Datenbanken verweist (Standarddatenbank immer zählt), wird für den Wert dieser Spalte ein Tabellenname mit der Datenbank qualifiziert.
Entsprechend sind die __Cluster-und Daten Bank__ Qualifizierungen im Wert vorhanden, wenn auf mehr als ein Cluster verwiesen wird. 
* `isfuzzy=``true`  |  `false` : Wenn `isfuzzy` auf festgelegt ist, `true` lässt die fuzzyauflösung von Union-Beinen zu. `Fuzzy` gilt für den Satz von `union` Quellen. Dies bedeutet, dass bei der Analyse der Abfrage und der Vorbereitung auf die Ausführung der Satz von Union-Quellen auf den Satz von Tabellen verweisen reduziert wird, der vorhanden und jederzeit zugänglich ist. Wenn mindestens eine solche Tabelle gefunden wurde, ergibt jeder Auflösungs Fehler eine Warnung in den Ergebnissen des Abfrage Status (eine für jeden fehlenden Verweis), verhindert jedoch die Abfrage Ausführung. Wenn keine Auflösungen erfolgreich waren, gibt die Abfrage einen Fehler zurück.
Der Standardwert lautet `isfuzzy=false`.

::: zone-end

## <a name="returns"></a>Gibt zurück

Eine Tabelle mit derselben Anzahl von Zeilen, wie in allen Eingabetabellen vorhanden sind.

**Hinweise**

::: zone pivot="azuredataexplorer"

1. `union`der Bereich kann [Let-Anweisungen](./letstatement.md) einschließen, wenn diese mit dem [View-Schlüsselwort](./letstatement.md) versehen sind
2. `union` der Bereich enthält keine [Funktionen](../management/functions.md). Definieren [Sie eine Let-Anweisung](./letstatement.md) mit dem View- [Schlüsselwort](./letstatement.md) , um eine Funktion in den Union-Bereich einzubeziehen.
3. Wenn `union` es sich bei der Eingabe um [Tabellen](../management/tables.md) handelt (im Vergleich zu [tabellarischen Ausdrücken](./tabularexpressionstatements.md)) und `union` auf den ein WHERE- [Operator](./whereoperator.md)folgt, um eine bessere Leistung zu [erzielen](./findoperator.md), empfiehlt es sich, beides durchsuchen zu ersetzen. Beachten Sie das andere [Ausgabe Schema](./findoperator.md#output-schema) , das vom-Operator erzeugt wird `find` . 
4. `isfuzzy=true` gilt nur für die `union` Auflösungsphase für Quellen. Sobald der Satz von Quell Tabellen bestimmt wird, werden mögliche zusätzliche Abfrage Fehler nicht unterdrückt.
5. Bei Verwendung `outer union` von enthält das Ergebnis alle Spalten, die in einer der Eingaben vorkommen, eine Spalte für jeden Namen und jede Art von vorkommen. Dies bedeutet Folgendes: Wenn eine Spalte in mehreren Tabellen angezeigt wird und mehrere Typen aufweist, enthält Sie eine entsprechende Spalte für jeden Typ im `union` Ergebnis. Dieser Spaltenname wird mit einem "_" versehen, gefolgt vom Ursprungs [Spaltentyp](./scalar-data-types/index.md).

::: zone-end

::: zone pivot="azuremonitor"

1. `union`der Bereich kann [Let-Anweisungen](./letstatement.md) einschließen, wenn diese mit dem [View-Schlüsselwort](./letstatement.md) versehen sind
2. `union` der Bereich enthält keine Funktionen. So schließen [Sie](./letstatement.md) eine Funktion in den Union-Bereich ein [view keyword](./letstatement.md)
3. Wenn `union` es sich bei der Eingabe um Tabellen handelt (im Vergleich zu [tabellarischen Ausdrücken](./tabularexpressionstatements.md)) und auf den `union` ein WHERE- [Operator](./whereoperator.md)folgt, empfiehlt es sich, beides durch die [Suche](./findoperator.md) zu verbessern Beachten Sie das andere [Ausgabe Schema](./findoperator.md#output-schema) , das vom-Operator erzeugt wird `find` . 
4. `isfuzzy=``true`gilt nur für die Phase der `union` Quellen Auflösung. Nachdem der Satz von Quell Tabellen ermittelt wurde, werden möglicherweise zusätzliche Abfrage Fehler nicht unterdrückt.
5. Bei Verwendung `outer union` von enthält das Ergebnis alle Spalten, die in einer der Eingaben vorkommen, eine Spalte für jeden Namen und jede Art von vorkommen. Dies bedeutet Folgendes: Wenn eine Spalte in mehreren Tabellen angezeigt wird und mehrere Typen aufweist, enthält Sie eine entsprechende Spalte für jeden Typ im `union` Ergebnis. Dieser Spaltenname wird mit einem "_" versehen, gefolgt vom Ursprungs [Spaltentyp](./scalar-data-types/index.md).

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>Beispiel: Tabellen mit Zeichenfolge in Name oder Spalte

```kusto
union K* | where * has "Kusto"
```

Zeilen aus allen Tabellen in der Datenbank, deren Name mit beginnt `K` und in dem eine beliebige Spalte das Wort enthält `Kusto` .

## <a name="example-distinct-count"></a>Beispiel: unterschiedliche Anzahl

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

Der Abfrage Status wird beobachtet: die folgende Warnung wurde zurückgegeben: `Failed to resolve entity 'View_3'`

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

Der Abfrage Status wird beobachtet: die folgende Warnung wurde zurückgegeben: `Failed to resolve entity 'SomeView*'`

**Beispiel: Quell Spaltentypen stimmen nicht überein.**
 
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

`x`Die Spalte aus `View_1` hat das Suffix empfangen `_long` , und wie eine Spalte `x_long` mit dem Namen bereits im Ergebnis Schema vorhanden ist, wurden die Spaltennamen dupliziert, und es wird eine neue Spalte erstellt.`x_long1`
