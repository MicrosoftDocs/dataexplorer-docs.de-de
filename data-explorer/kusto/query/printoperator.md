---
title: 'Druck Operator: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt den Druck Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: 9a5a780a6f7bdf277566d1c0421c5ca2a3a93602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346064"
---
# <a name="print-operator"></a>print-Operator

Gibt eine einzelne Zeile mit einem oder mehreren skalaren Ausdrücken aus.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

## <a name="syntax"></a>Syntax

`print`[*ColumnName* `=` ] *Scalarexpression* [', '...]

## <a name="arguments"></a>Argumente

* *ColumnName*: ein Options Name, der der Singular-Spalte der Ausgabe zugewiesen werden soll.
* *Scalarexpression*: ein auszuwertender Skalarausdruck.

## <a name="returns"></a>Gibt zurück

Eine einspaltige Tabelle mit einer einzelnen Zeile, deren einzelne Zelle den Wert des ausgewerteten *scalarexpression*aufweist.

## <a name="examples"></a>Beispiele

Der- `print` Operator ist eine schnelle Möglichkeit, um einen oder mehrere skalare Ausdrücke auszuwerten und aus den resultierenden Werten eine einzeilige Tabelle zu erstellen.
Beispiel:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print banner=strcat("Hello", ", ", "World!")
```
