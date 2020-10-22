---
title: 'Project-Away-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Projekt entfernten Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2938019237c882891af8ff86f4d33de3605a9063
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356519"
---
# <a name="project-away-operator"></a>project-away-Operator

Wählen Sie aus, welche Spalten aus der Eingabe aus der Ausgabe ausgeschlossen werden sollen.

```kusto
T | project-away price, quantity, zz*
```

Die Reihenfolge der Spalten im Ergebnis wird durch ihre ursprüngliche Reihenfolge in der Tabelle bestimmt. Nur die Spalten, die als Argumente angegeben wurden, werden gelöscht. Die anderen Spalten sind im Ergebnis enthalten. (Siehe auch `project`.)

## <a name="syntax"></a>Syntax

*T* `| project-away` *columnnameorpattern* [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: die Eingabe Tabelle
* *Columnnameorpattern:* Der Name des Spalten-oder Spalten Platzhalter Musters, das aus der Ausgabe entfernt werden soll.

## <a name="returns"></a>Rückgabe

Eine Tabelle mit Spalten, die nicht als Argumente benannt wurden. Enthält die gleiche Anzahl von Zeilen wie die Eingabe Tabelle.

> [!TIP]
>
> * Verwenden Sie zum Umbenennen von Spalten [`project-rename`](projectrenameoperator.md) .
> * Verwenden Sie, um Spalten neu anzuordnen [`project-reorder`](projectreorderoperator.md) .
> * `project-away`Alle Spalten, die in der ursprünglichen Tabelle vorhanden sind oder als Teil der Abfrage berechnet wurden, sind möglich.

## <a name="examples"></a>Beispiele

Die Eingabetabelle `T` umfasst drei Spalten vom Typ `long`: `A`, `B` und `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|A|B|
|---|---|
|1|2|

Entfernen von Spalten, die mit "a" beginnen.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a2='a2', b = 'b', a3='a3', a1='a1'
| project-away a*
```

|k|
|---|
|k|

## <a name="see-also"></a>Weitere Informationen

Um auszuwählen, welche Spalten aus der Eingabe in der Ausgabe aufbewahrt werden sollen, verwenden Sie [Project-Keep](project-keep-operator.md).
