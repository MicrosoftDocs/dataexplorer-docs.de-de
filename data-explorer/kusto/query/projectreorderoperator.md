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
ms.openlocfilehash: de8ff4b9256c8f964350bafd64eac15b028f53d4
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356485"
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
> * Verwenden Sie zum Entfernen von Spalten [`project-away`](projectawayoperator.md) .
> * Um auszuwählen, welche Spalten aufbewahrt werden sollen, verwenden Sie [`project-keep`](project-keep-operator.md) .
> * Verwenden Sie zum Umbenennen von Spalten [`project-rename`](projectrenameoperator.md) .

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
