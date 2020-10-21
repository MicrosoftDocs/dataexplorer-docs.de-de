---
title: 'Project-reorder-Operator: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt den Projekttyp-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7bcb33d30bdfdbd22b28dbb7364427cfa3a81a5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242142"
---
# <a name="project-reorder-operator"></a>project-reorder-Operator

Ordnet Spalten in der Ergebnis Ausgabe neu an.

```kusto
T | project-reorder Col2, Col1, Col* asc
```

## <a name="syntax"></a>Syntax

*T* `| project-reorder` *columnnameorpattern* [ `asc` | `desc` ] [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: die Eingabe Tabelle.
* *Columnnameorpattern:* Der Name des Spalten-oder Spalten Platzhalter Musters, das der Ausgabe hinzugefügt wird.
* Für Platzhalter Muster: angeben `asc` oder `desc` Sortieren von Spalten mit ihren Namen in aufsteigender oder absteigender Reihenfolge. Wenn `asc` oder `desc` nicht angegeben ist, wird die Reihenfolge durch die übereinstimmenden Spalten bestimmt, wie Sie in der Quell Tabelle angezeigt werden.

> [!NOTE]
> * Bei einem mehrdeutigen *columnnameorpattern* -Abgleich wird die Spalte an der ersten Position angezeigt, die mit dem Muster übereinstimmt.
> * Das Angeben von Spalten für `project-reorder` ist optional. Spalten, die nicht explizit angegeben werden, werden als letzte Spalten der Ausgabe Tabelle angezeigt.
> * Verwenden [`project-away`](projectawayoperator.md) Sie, um Spalten zu entfernen.
> * Verwenden [`project-rename`](projectrenameoperator.md) Sie zum Umbenennen von Spalten.


## <a name="returns"></a>Rückgabe

Eine Tabelle, die Spalten in der von den Operator Argumenten angegebenen Reihenfolge enthält. `project-reorder` benennt oder entfernt keine Spalten aus der Tabelle. aus diesem Grund werden alle Spalten, die in der Quell Tabelle vorhanden waren, in der Ergebnistabelle angezeigt.

## <a name="examples"></a>Beispiele

Ordnen Sie eine Tabelle mit drei Spalten (a, b, c) neu an, damit die zweite Spalte (b) zuerst angezeigt wird.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-reorder b
```

|k|a|c|
|---|---|---|
|k|a|c|

Ordnen Sie Spalten einer Tabelle so an, dass Spalten, die mit beginnen, `a` vor anderen Spalten angezeigt werden.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print b = 'b', a2='a2', a3='a3', a1='a1'
|  project-reorder a* asc
```

|a1|a2|a3|k|
|---|---|---|---|
|a1|a2|a3|k|
