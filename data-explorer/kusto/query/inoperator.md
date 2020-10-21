---
title: 'in-und NOTIN "-Operatoren: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Operatoren in und NOTIN "in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 52e26bb5564079de56817e75a203a400ef7f1a50
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248997"
---
# <a name="in-and-in-operators"></a>in- und !in-Operatoren

Filtert einen Daten Satz Satz basierend auf dem bereitgestellten Satz von Werten.

```kusto
Table1 | where col in ('value1', 'value2')
```

> [!NOTE]
> * Durch das Hinzufügen von ' ~ ' zum Operator wird die Groß-/Kleinschreibung nicht beachtet: `x in~ (expression)` oder `x !in~ (expression)` .
> * In tabellarischen Ausdrücken wird die erste Spalte des Resultsets ausgewählt.
> * Die Ausdrucks Liste kann bis zu `1,000,000` Werte liefern.
> * In einer einzelnen Werteliste werden die in der Liste befindlichen Arrays vereinfacht. `x in (dynamic([1,[2,3]]))` wird beispielsweise zu `x in (1,2,3)`.
 
## <a name="syntax"></a>Syntax

### <a name="case-sensitive-syntax"></a>Syntax der Groß-/Kleinschreibung

*T* - `|` `where` *col* `in` `(` *Liste der Skalarausdrücke*`)`   
*T* - `|` `where` *col* `in` `(` *Tabellen Ausdruck (Tabellen* )`)`   
 
*T* - `|` `where` *col* `!in` `(` *Liste der Skalarausdrücke*`)`  
*T* - `|` `where` *col* `!in` `(` *Tabellen Ausdruck (Tabellen* )`)`   

### <a name="case-insensitive-syntax"></a>Syntax ohne Beachtung

*T* - `|` `where` *col* `in~` `(` *Liste der Skalarausdrücke*`)`   
*T* - `|` `where` *col* `in~` `(` *Tabellen Ausdruck (Tabellen* )`)`   
 
*T* - `|` `where` *col* `!in~` `(` *Liste der Skalarausdrücke*`)`  
*T* - `|` `where` *col* `!in~` `(` *Tabellen Ausdruck (Tabellen* )`)`   

## <a name="arguments"></a>Argumente

* *T* : die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *Col* : die zu filternde Spalte.
* eine *Liste von Ausdrücken* : eine durch Trennzeichen getrennte Liste von tabellarischen, skalaren oder literalen Ausdrücken.
* *tabellarischer Ausdruck* : ein tabellarischer Ausdruck, der über einen Satz von Werten verfügt. Wenn der Ausdruck über mehrere Spalten verfügt, wird die erste Spalte verwendet.

## <a name="returns"></a>Rückgabe

Zeilen in *T* , für die das Prädikat ist `true` .

## <a name="examples"></a>Beispiele  

### <a name="use-in-operator"></a>Verwenden des "in"-Operators

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|4775|  

### <a name="use-in-operator"></a>Use ' in ~ '-Operator  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Anzahl|
|---|
|4775|  

### <a name="use-in-operator"></a>Use '! in '-Operator

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|54291|  


### <a name="use-dynamic-array"></a>Dynamisches Array verwenden

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

### <a name="subquery"></a>Unterabfrage

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

### <a name="top-with-other-example"></a>Top mit anderem Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| Zustand     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| Wisconsin | 31                   |
| TEXAS     | 55                   |
| Flori   | 85                   |
| Georgien   | 106                  |
| Sonstiges     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>Verwenden einer statischen Liste, die von einer Funktion zurückgegeben wird

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Anzahl|
|---|
|4775|  

Die Funktionsdefinition.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|Name|Parameter|Text|Ordner|DocString|
|---|---|---|---|---|
|Interessantheits Zustände|()|{Dynamic (["Washington", "Florida", "Georgia", "New York"])}
