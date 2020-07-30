---
title: 'Project-Away-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Projekt entfernten Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 40bc5eafee803123ea1d73e763c32b5210f741ca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346047"
---
# <a name="project-away-operator"></a>project-away-Operator

Wählen Sie aus, welche Spalten in der Eingabe aus der Ausgabe ausgeschlossen werden sollen.

```kusto
T | project-away price, quantity, zz*
```

Die Reihenfolge der Spalten im Ergebnis wird durch ihre ursprüngliche Reihenfolge in der Tabelle bestimmt. Nur die Spalten, die als Argumente angegeben wurden, werden gelöscht. Die anderen Spalten sind im Ergebnis enthalten.  (Siehe auch `project`.)

## <a name="syntax"></a>Syntax

*T* `| project-away` *columnnameorpattern* [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: die Eingabe Tabelle
* *Columnnameorpattern:* Der Name des Spalten-oder Spalten Platzhalter Musters, das aus der Ausgabe entfernt werden soll.

## <a name="returns"></a>Rückgabe

Eine Tabelle mit Spalten, die nicht als Argumente benannt wurden. Enthält die gleiche Anzahl von Zeilen wie die Eingabe Tabelle.

**Tipps**

* Verwenden [`project-rename`](projectrenameoperator.md) Sie, wenn Sie beabsichtigen, Spalten umzubenennen.
* Verwenden [`project-reorder`](projectreorderoperator.md) Sie, wenn Sie beabsichtigen, Spalten neu anzuordnen.

* `project-away`Alle Spalten, die in der ursprünglichen Tabelle vorhanden sind oder als Teil der Abfrage berechnet wurden, sind möglich.


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
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

