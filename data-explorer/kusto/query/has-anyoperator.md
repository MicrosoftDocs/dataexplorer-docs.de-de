---
title: 'has_any-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird has_any Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 19329b8822a1e1d484c5f751f5fbc2f8eb6343ac
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226736"
---
# <a name="has_any-operator"></a>has_any-Operator

`has_any`Operator Filter basierend auf dem bereitgestellten Satz von Werten.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**Syntax**

*T* - `|` `where` *col* `has_any` `(` *Liste der Skalarausdrücke*`)`   
*T* - `|` `where` *col* `has_any` `(` *Tabellen Ausdruck (Tabellen* )`)`   
 
**Argumente**

* *T* -tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *col* zu filternde Spalten Spalte.
* *Liste der Ausdrücke* : durch Trennzeichen getrennte Liste von Tabellen-, Skalar-oder Literalausdrücken  
* *tabellarischer Ausdruck* -Tabellen Ausdruck, der einen Satz von Werten aufweist (wenn Expression über mehrere Spalten verfügt, wird die erste Spalte verwendet)

**Rückgabe**

Zeilen in *T* , für die das Prädikat`true`

**Hinweise**

* Die Ausdrucks Liste kann bis zu `10,000` Werte liefern.    
* Bei tabellarischen Ausdrücken wird die erste Spalte des Resultsets ausgewählt.   

**Beispiele:**  

**Einfache Verwendung des `has_any` Operators:**  

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|Bundesland/Kanton|count_|
|---|---|
|NEW YORK|1750|
|North Carolina|1721|
|South Dakota|1567|
|New-Jersey|1044|
|South Carolina|915|
|North Dakota|905|
|New Mexico|527|
|New Hampshire|394|


**Verwenden des dynamischen Arrays:**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|Bundesland/Kanton|count_|
|---|---|
|North Carolina|1721|
|South Dakota|1567|
|South Carolina|915|
|North Dakota|905|
|Atlantik, Süden|193|
|Atlantik, Norden|188|
