---
title: 'in-und NOTIN "-Operatoren: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Operatoren in und NOTIN "in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: cd11362c15e5ecfb80eab57b57b22f190f47da05
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271569"
---
# <a name="in-and-in-operators"></a>in- und !in-Operatoren

Filtert ein Recordset basierend auf dem bereitgestellten Satz von Werten.

```kusto
Table1 | where col in ('value1', 'value2')
```

**Syntax**

*Groß-/Kleinschreibung beachten:*

*T* - `|` `where` *col* `in` `(` *Liste der Skalarausdrücke*`)`   
*T* - `|` `where` *col* `in` `(` *Tabellen Ausdruck (Tabellen* )`)`   
 
*T* - `|` `where` *col* `!in` `(` *Liste der Skalarausdrücke*`)`  
*T* - `|` `where` *col* `!in` `(` *Tabellen Ausdruck (Tabellen* )`)`   

*Syntax zur Groß-/Kleinschreibung*

*T* - `|` `where` *col* `in~` `(` *Liste der Skalarausdrücke*`)`   
*T* - `|` `where` *col* `in~` `(` *Tabellen Ausdruck (Tabellen* )`)`   
 
*T* - `|` `where` *col* `!in~` `(` *Liste der Skalarausdrücke*`)`  
*T* - `|` `where` *col* `!in~` `(` *Tabellen Ausdruck (Tabellen* )`)`   

**Argumente**

* *T* : die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *Col* : die zu filternde Spalte.
* *Liste der Ausdrücke* : eine durch Trennzeichen getrennte Liste von tabellarischen, skalaren oder literalen Ausdrücken.  
* *tabellarischer Ausdruck* : ein tabellarischer Ausdruck, der einen Satz von Werten aufweist (in einem Case-Ausdruck gibt es mehrere Spalten, die erste Spalte wird verwendet).

**Rückgabe**

Zeilen in *T* , für die das Prädikat`true`

**Hinweise**

* Die Ausdrucks Liste kann bis zu Werte liefern. `1,000,000`    
* Die schsted Arrays werden in einer einzelnen Werteliste vereinfacht, z. b. `x in (dynamic([1,[2,3]]))` in`x in (1,2,3)` 
* Bei tabellarischen Ausdrücken wird die erste Spalte des Resultsets ausgewählt.   
* Durch das Hinzufügen von ' ~ ' zum Operator wird die Groß-/Kleinschreibung nicht beachtet: `x in~ (expression)` oder `x !in~ (expression)` .

**Beispiele:**  

**Einfache Verwendung des "in"-Operators:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|4775|  


**Einfache Verwendung von "in ~"-Operator:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Anzahl|
|---|
|4775|  

**Einfache Verwendung des Operators "! in":**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|54291|  


**Verwenden des dynamischen Arrays:**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Anzahl|
|---|
|3218|


**Beispiel für eine Unterabfrage:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Using subquery
let Top_5_States = 
StormEvents
| summarize count() by State
| top 5 by count_; 
StormEvents 
| where State in (Top_5_States) 
| count
```

Dieselbe Abfrage kann wie folgt geschrieben werden:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Inline subquery 
StormEvents 
| where State in (
    ( StormEvents
    | summarize count() by State
    | top 5 by count_ )
) 
| count
```

|Anzahl|
|---|
|14242|  

**Top mit anderem Beispiel:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| Bundesland/Kanton     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| Wisconsin | 31                   |
| TEXAS     | 55                   |
| Flori   | 85                   |
| Georgien   | 106                  |
| Andere     | 415                  |

**Verwenden einer statischen Liste, die von einer Funktion zurückgegeben wird:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Anzahl|
|---|
|4775|  


Dies ist die Funktionsdefinition:  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|name|Parameter|Body|Ordner|DocString|
|---|---|---|---|---|
|Interessantheits Zustände|()|{Dynamic (["Washington", "Florida", "Georgia", "New York"])}
