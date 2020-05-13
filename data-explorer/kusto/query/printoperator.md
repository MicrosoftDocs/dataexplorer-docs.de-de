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
ms.openlocfilehash: d5788ee937fe110b63a8f137fdab0790eb7cb37e
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373202"
---
# <a name="print-operator"></a>print-Operator

Gibt eine einzelne Zeile mit einem oder mehreren skalaren Ausdrücken aus.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**Syntax**

`print`[*ColumnName* `=` ] *Scalarexpression* [', '...]

**Argumente**

* *ColumnName*: ein Options Name, der der Singular-Spalte der Ausgabe zugewiesen werden soll.
* *Scalarexpression*: ein auszuwertender Skalarausdruck.

**Rückgabe**

Eine einspaltige Tabelle mit einer einzelnen Zeile, deren einzelne Zelle den Wert des ausgewerteten *scalarexpression*aufweist.

**Beispiele**

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
