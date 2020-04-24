---
title: Druckoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Druckoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: b751b07446a74ef17002abac26e4c881a2da757b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510964"
---
# <a name="print-operator"></a>print-Operator

Gibt eine Zeile mit einem oder mehreren skalaren Ausdrücken aus.

```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**Syntax**

`print`[*Spaltenname* `=`] *ScalarExpression* [',' ...]

**Argumente**

* *ColumnName*: Ein Optionsname, der der Singularspalte der Ausgabe zugewiesen werden soll.
* *ScalarExpression*: Ein skalarer Ausdruck, der ausgewertet werden soll.

**Rückgabe**

Eine einspaltige, einzeilige Tabelle, deren einzelne Zelle den Wert der *ausgewerteten ScalarExpression*hat.

**Beispiele**

Der `print` Operator ist nützlich, um einen oder mehrere skalare Ausdrücke schnell auszuwerten und aus den resultierenden Werten eine einzeilige Tabelle zu erstellen.
Beispiel:

```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```

```kusto
print banner=strcat("Hello", ", ", "World!")
```