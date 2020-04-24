---
title: has_any-Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird has_any Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 42a181f7c75ef36119f7f2915fd5327d4a656eb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514313"
---
# <a name="has_any-operator"></a>has_any-Operator

`has_any`Bedienerfilter basierend auf dem angegebenen Wertesatz.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

**Syntax**

*T* `|` T `where` *col* `has_any` col `(` *Liste skalarer Ausdrücke*`)`   
*T* `|` *col* `has_any` `(`kolularer *Ausdruck* `where``)`   
 
**Argumente**

* *T* - Tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *col* - Spalte zum Filtern.
* *Liste der Ausdrücke* - Durch Kommas getrennte Liste von tabellenförmigen, skalaren oder literalen Ausdrücken  
* *Tabellarischer Ausdruck* - Tabellenausdruck mit einem Satz von Werten (wenn Ausdruck mehrere Spalten enthält, wird die erste Spalte verwendet)

**Rückgabe**

Zeilen in *T,* für die das Prädikat`true`

**Hinweise**

* Die Ausdrucksliste kann `10,000` bis zu Werten erzeugen.    
* Bei tabellarischen Ausdrücken wird die erste Spalte des Resultsets ausgewählt.   

**Beispiele:**  

**Eine einfache `has_any` Verwendung des Bedieners:**  

```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|State|count_|
|---|---|
|NEW YORK|1750|
|NORTH CAROLINA|1721|
|SOUTH DAKOTA|1567|
|NEW JERSEY|1044|
|SOUTH CAROLINA|915|
|NORTH DAKOTA|905|
|NEW MEXICO|527|
|NEW HAMPSHIRE|394|


**Verwenden eines dynamischen Arrays:**

```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|State|count_|
|---|---|
|NORTH CAROLINA|1721|
|SOUTH DAKOTA|1567|
|SOUTH CAROLINA|915|
|NORTH DAKOTA|905|
|ATLANTIC SOUTH|193|
|ATLANTIC NORTH|188|