---
title: 'in- und notin-Operatoren: Azure Data Explorer'
description: In diesem Artikel werden die in- und notin-Operatoren in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.localizationpriority: high
ms.openlocfilehash: ffb24abe744bfbe3f7f95336edf0263becfa7ec9
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513249"
---
# <a name="in-and-in-operators"></a>in- und !in-Operatoren

Filtert einen Datensatz basierend auf dem bereitgestellten Satz von Werten.

```kusto
Table1 | where col in ('value1', 'value2')
```

> [!NOTE]
> * Durch das Hinzufügen von „~“ zum Operator wird bei der Suche des Werts Groß-/Kleinschreibung nicht beachtet: `x in~ (expression)` oder `x !in~ (expression)`.
> * In tabellarischen Ausdrücken wird die erste Spalte des Resultsets ausgewählt.
> * Die Ausdrucksliste kann bis zu `1,000,000` Werte generieren.
> * Geschachtelte Arrays werden in einer einzigen Liste von Werten vereinfacht. `x in (dynamic([1,[2,3]]))` wird beispielsweise zu `x in (1,2,3)`.
 
## <a name="syntax"></a>Syntax

### <a name="case-sensitive-syntax"></a>Syntax für Beachtung von Groß-/Kleinschreibung

*T* `|` `where` *col* `in` `(`*Liste mit Skalarausdrücken*`)`   
*T* `|` `where` *col* `in` `(`*tabularischer Ausdruck*`)`   
 
*T* `|` `where` *col* `!in` `(`*Liste mit Skalarausdrücken*`)`  
*T* `|` `where` *col* `!in` `(`*tabularischer Ausdruck*`)`   

### <a name="case-insensitive-syntax"></a>Syntax für Nichtbeachtung von Groß-/Kleinschreibung

*T* `|` `where` *col* `in~` `(`*Liste mit Skalarausdrücken*`)`   
*T* `|` `where` *col* `in~` `(`*tabularischer Ausdruck*`)`   
 
*T* `|` `where` *col* `!in~` `(`*Liste mit Skalarausdrücken*`)`  
*T* `|` `where` *col* `!in~` `(`*tabularischer Ausdruck*`)`   

## <a name="arguments"></a>Argumente

* *T*: Die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *col*: Die zu filternde Spalte.
* *Liste mit Ausdrücken*: Eine durch Trennzeichen getrennte Liste von tabellarischen, Skalar- oder Literalausdrücken.
* *tabellarischer Ausdruck*: Ein tabellarischer Ausdruck, der einen Satz von Werten aufweist. Wenn der Ausdruck über mehrere Spalten verfügt, wird die erste Spalte verwendet.

## <a name="returns"></a>Gibt zurück

Zeilen in *T*, für die das Prädikat `true` ist.

## <a name="examples"></a>Beispiele  

### <a name="use-in-operator"></a>Verwendung des in-Operators

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|4775|  

### <a name="use-in-operator"></a>Verwendung des in~-Operators  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Anzahl|
|---|
|4775|  

### <a name="use-in-operator"></a>Verwendung des !in-Operators

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|54291|  


### <a name="use-dynamic-array"></a>Verwendung eines dynamischen Arrays

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

| State     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| WISCONSIN | 31                   |
| TEXAS     | 55                   |
| FLORIDA   | 85                   |
| GEORGIA   | 106                  |
| Andere     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>Verwendung einer statischen Liste, die von einer Funktion zurückgegeben wird

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
|InterestingStates|()|{ dynamic(["WASHINGTON", "FLORIDA", "GEORGIA", "NEW YORK"]) }
