---
title: column_ifexists() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird column_ifexists() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4f7d4a2114aa2b9b3bb8ae3e951306a3ffb725e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517169"
---
# <a name="column_ifexists"></a>column_ifexists()

Verwendet einen Spaltennamen als Zeichenfolge und einen Standardwert. Gibt einen Verweis auf die Spalte zurück, falls vorhanden, andernfalls - gibt den Standardwert zurück.

**Syntax**

`column_ifexists(`*columnName*`, `*defaultValue*)

**Argumente**

* *columnName*: Der Name der Spalte
* *defaultValue*: Der Wert, der verwendet werden soll, wenn die Spalte nicht in dem Kontext vorhanden ist, in dem die Funktion verwendet wurde.
                  Dieser Wert kann ein beliebiger skalader Ausdruck sein (z. B. ein Verweis auf eine andere Spalte).

**Rückgabe**

Wenn *columnName* vorhanden ist, dann die Spalte, auf die sie verweist. Andernfalls - *defaultValue*.

**Beispiele**

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```