---
title: in- und notin-Operatoren - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden In- und Notin-Operatoren in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: bd247de2bd211ae7be3da449e940899d2e8bb475
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513803"
---
# <a name="in-and-in-operators"></a>in- und !in-Operatoren

Filtert ein Recordset basierend auf dem angegebenen Wertesatz.

```kusto
Table1 | where col in ('value1', 'value2')
```

**Syntax**

*Syntax zu Groß-/Kleinschreibung:*

*T* `|` T `where` *col* `in` col `(` *Liste skalarer Ausdrücke*`)`   
*T* `|` *col* `in` `(`kolularer *Ausdruck* `where``)`   
 
*T* `|` T `where` *col* `!in` col `(` *Liste skalarer Ausdrücke*`)`  
*T* `|` *col* `!in` `(`kolularer *Ausdruck* `where``)`   

*Groß-/Kleinschreibung, bei der die Syntax nicht berücksichtigt wird:*

*T* `|` T `where` *col* `in~` col `(` *Liste skalarer Ausdrücke*`)`   
*T* `|` *col* `in~` `(`kolularer *Ausdruck* `where``)`   
 
*T* `|` T `where` *col* `!in~` col `(` *Liste skalarer Ausdrücke*`)`  
*T* `|` *col* `!in~` `(`kolularer *Ausdruck* `where``)`   

**Argumente**

* *T* - Die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *col* - die zu filternde Spalte.
* *Liste der Ausdrücke* - eine durch Kommas getrennte Liste von tabellenförmigen, skalaren oder literalen Ausdrücken  
* *Tabellarischer Ausdruck* - ein tabellarischer Ausdruck, der einen Satz von Werten enthält (in einem Fallausdruck, der mehrere Spalten enthält, wird die erste Spalte verwendet)

**Rückgabe**

Zeilen in *T,* für die das Prädikat`true`

**Hinweise**

* Die Ausdrucksliste kann `1,000,000` bis zu Werten erzeugen    
* Verschachtelte Arrays werden in eine einzige Werteliste `x in (dynamic([1,[2,3]]))` abgeflacht, z. B.`x in (1,2,3)` 
* Bei tabellarischen Ausdrücken wird die erste Spalte des Resultsets   
* Wenn Sie dem Operator ''' hinzufügen, `x in~ (expression)` `x !in~ (expression)`wird die Suchanfrage von Werten unbeachtet: oder .

**Beispiele:**  

**Eine einfache Verwendung des 'in'-Operators:**  

```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|4775|  


**Eine einfache Verwendung des Operators 'in'::**  

```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Anzahl|
|---|
|4775|  

**Eine einfache Verwendung des '!in'-Operators:**  

```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Anzahl|
|---|
|54291|  


**Verwenden eines dynamischen Arrays:**
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Anzahl|
|---|
|3218|


**Ein Beispiel für eine Unterabfrage:**  

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

Die gleiche Abfrage kann wie:

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

**Top mit anderen Beispielen:**  

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
| Wisconsin | 31                   |
| TEXAS     | 55                   |
| Florida   | 85                   |
| Georgien   | 106                  |
| Andere     | 415                  |

**Verwenden einer statischen Liste, die von einer Funktion zurückgegeben wird:**  

```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Anzahl|
|---|
|4775|  


Hier ist die Funktionsdefinition:  

```kusto
.show function InterestingStates
```

|name|Parameter|Body|Ordner|DocString|
|---|---|---|---|---|
|InteressanteStaaten|()|• dynamic(["WASHINGTON", "FLORIDA", "GEORGIA", "NEW YORK"])
