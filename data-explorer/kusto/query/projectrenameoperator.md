---
title: Projektumbenennungsoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Projektumbenennungsoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f7fbf2efbfd3ed1dfac9129ec21f5a13c51481c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510862"
---
# <a name="project-rename-operator"></a>project-rename-Operator

Benennt Spalten in der Ergebnisausgabe um.

```kusto
T | project-rename new_column_name = column_name
```

**Syntax**

*T* `| project-rename` *NewColumnName* = *ExistingColumnName* [`,` ...]

**Argumente**

* *T*: Die Eingabetabelle.
* *NewColumnName:* Der neue Name einer Spalte. 
* *ExistingColumnName:* Der vorhandene Name einer Spalte. 

**Rückgabe**

Eine Tabelle mit den Spalten in der gleichen Reihenfolge wie in einer vorhandenen Tabelle, deren Spalten umbenannt werden.


**Beispiele**

```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|