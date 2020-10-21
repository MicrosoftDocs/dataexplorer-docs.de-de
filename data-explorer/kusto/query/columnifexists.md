---
title: column_ifexists ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird column_ifexists () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570a832673d94d57d69dfa9c8442d56802ecd7ac
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247861"
---
# <a name="column_ifexists"></a>column_ifexists()

Nimmt einen Spaltennamen als Zeichenfolge und einen Standardwert an. Gibt einen Verweis auf die Spalte zurück, wenn Sie vorhanden ist, andernfalls wird der Standardwert zurückgegeben.

## <a name="syntax"></a>Syntax

`column_ifexists(`*ColumnName* `, ` *DefaultValue*)

## <a name="arguments"></a>Argumente

* *ColumnName*: der Name der Spalte.
* *DefaultValue*: der Wert, der verwendet werden soll, wenn die Spalte nicht im Kontext vorhanden ist, in dem die Funktion verwendet wurde.
                  Dieser Wert kann ein beliebiger skalarer Ausdruck sein (z. b. ein Verweis auf eine andere Spalte).

## <a name="returns"></a>Rückgabe

Wenn *ColumnName* vorhanden ist, wird die Spalte, auf die Sie verweist, angezeigt. Andernfalls- *DefaultValue*.

## <a name="examples"></a>Beispiele

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```