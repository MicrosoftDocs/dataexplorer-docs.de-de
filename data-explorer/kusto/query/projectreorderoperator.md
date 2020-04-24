---
title: Projektneuordnungsoperator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Projektneuordnungsoperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf56a69891f83aaabc12dbbcd8f758ecb963b493
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510845"
---
# <a name="project-reorder-operator"></a>project-reorder-Operator

Ordnet Spalten in der Ergebnisausgabe neu an.

```kusto
T | project-reorder Col2, Col1, Col* asc
```

**Syntax**

*T* `| project-reorder` *ColumnNameOrPattern* [`asc`|`desc`] [`,` ...]

**Argumente**

* *T*: Die Eingabetabelle.
* *ColumnNameOrPattern:* Der Name des Spalten- oder Spaltenplatzhaltermusters, das der Ausgabe hinzugefügt wurde.
* Für Platzhaltermuster: `asc` `desc` Geben sie Spalten mit ihren Namen in aufsteigender oder absteigender Reihenfolge an oder ordnet sie an. Wenn `asc` `desc` oder nicht angegeben, wird die Reihenfolge durch die übereinstimmenden Spalten bestimmt, wie sie in der Quelltabelle angezeigt werden.

**Rückgabe**

Eine Tabelle, die Spalten in der von den Operatorargumenten angegebenen Reihenfolge enthält. `project-reorder`werden Spalten nicht umbenannt oder aus der Tabelle entfernt, daher werden alle Spalten, die in der Quelltabelle vorhanden waren, in der Ergebnistabelle angezeigt.

**Hinweise**

- Bei *mehrdeutigecolumncolumnnamensOrPattern-Übereinstimmung* wird die Spalte an der ersten Position angezeigt, die dem Muster entspricht.
- Das Angeben von `project-reorder` Spalten für die ist optional. Spalten, die nicht angegeben sind, werden explizit als letzte Spalten der Ausgabetabelle angezeigt.

* Verwenden [`project-away`](projectawayoperator.md) Sie diese Datei zum Entfernen von Spalten.
* Verwenden [`project-rename`](projectrenameoperator.md) Sie diese Datei, um Spalten umzubenennen.


**Beispiele**

Ordnen Sie eine Tabelle mit drei Spalten (a, b, c) neu an, damit zuerst die zweite Spalte (b) angezeigt wird.

```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|b|a|c|
|---|---|---|
|b|a|c|

Ordnen Sie Spalten einer Tabelle neu `a` an, sodass Spalten, die mit beginnen, vor anderen Spalten angezeigt werden.

```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|a1|a2|a3|b|
|---|---|---|---|
|a1|a2|a3|b|