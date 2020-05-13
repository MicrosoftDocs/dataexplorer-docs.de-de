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
ms.openlocfilehash: 444710775af405cc63193e0205e573b2ea77de3a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373190"
---
# <a name="project-away-operator"></a>project-away-Operator

Wählen Sie aus, welche Spalten in der Eingabe aus der Ausgabe ausgeschlossen werden sollen.

```kusto
T | project-away price, quantity, zz*
```

Die Reihenfolge der Spalten im Ergebnis wird durch ihre ursprüngliche Reihenfolge in der Tabelle bestimmt. Nur die Spalten, die als Argumente angegeben wurden, werden gelöscht. Die anderen Spalten sind im Ergebnis enthalten.  (Siehe auch `project`.)

**Syntax**

*T* `| project-away` *columnnameorpattern* [ `,` ...]

**Argumente**

* *T*: die Eingabe Tabelle
* *Columnnameorpattern:* Der Name des Spalten-oder Spalten Platzhalter Musters, das aus der Ausgabe entfernt werden soll.

**Rückgabe**

Eine Tabelle mit Spalten, die nicht als Argumente benannt wurden. Enthält die gleiche Anzahl von Zeilen wie die Eingabe Tabelle.

**Tipps**

* Verwenden [`project-rename`](projectrenameoperator.md) Sie, wenn Sie beabsichtigen, Spalten umzubenennen.
* Verwenden [`project-reorder`](projectreorderoperator.md) Sie, wenn Sie beabsichtigen, Spalten neu anzuordnen.

* `project-away`Alle Spalten, die in der ursprünglichen Tabelle vorhanden sind oder als Teil der Abfrage berechnet wurden, sind möglich.


**Beispiele**

Die Eingabetabelle `T` umfasst drei Spalten vom Typ `long`: `A`, `B` und `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|Ein|B|
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

