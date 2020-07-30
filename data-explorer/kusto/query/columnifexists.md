---
title: column_ifexists ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird column_ifexists () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5eeecf9e4756ac18cdeb5c6297aea1bcca5bac14
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348852"
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