---
title: 'Project-Rename-Operator: Azure Daten-Explorer'
description: In diesem Artikel wird der Projekt Umbenennungs Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68581cfe4b3828823ced7d4704eb08df5d2aefa7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373174"
---
# <a name="project-rename-operator"></a>project-rename-Operator

Benennt Spalten in der Ergebnis Ausgabe um.

```kusto
T | project-rename new_column_name = column_name
```

**Syntax**

*T* `| project-rename` *newcolumnname*  =  *existingcolumnname* [ `,` ...]

**Argumente**

* *T*: die Eingabe Tabelle.
* *Newcolumnname:* Der neue Name einer Spalte. 
* *Existingcolumnname:* Der vorhandene Name einer Spalte. 

**Rückgabe**

Eine Tabelle, in der die Spalten in derselben Reihenfolge wie in einer vorhandenen Tabelle mit umbenannten Spalten vorhanden sind.


**Beispiele**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
