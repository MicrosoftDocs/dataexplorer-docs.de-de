---
title: Projekt-Auswärts-Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Projektwegoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 38ec57e9659458ef34117e4a380c756310db218b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510947"
---
# <a name="project-away-operator"></a>project-away-Operator

Wählen Sie aus, welche Spalten in der Eingabe von der Ausgabe ausgeschlossen werden sollen.

```kusto
T | project-away price, quantity, zz*
```

Die Reihenfolge der Spalten im Ergebnis wird durch ihre ursprüngliche Reihenfolge in der Tabelle bestimmt. Nur die Spalten, die als Argumente angegeben wurden, werden gelöscht. Die anderen Spalten sind im Ergebnis enthalten.  (Siehe auch `project`.)

**Syntax**

*T* `| project-away` *ColumnNameOrPattern* [`,` ...]

**Argumente**

* *T*: Die Eingabetabelle
* *ColumnNameOrPattern:* Der Name des Platzhaltermusters der Spalte oder Spalte, das aus der Ausgabe entfernt werden soll.

**Rückgabe**

Eine Tabelle mit Spalten, die nicht als Argumente benannt wurden. Enthält die gleiche Anzahl von Zeilen wie die Eingabetabelle.

**Tipps**

* Verwenden [`project-rename`](projectrenameoperator.md) Sie diese Verwendung, wenn Sie Spalten umbenennen möchten.
* Verwenden [`project-reorder`](projectreorderoperator.md) Sie diese Verwendung, wenn Sie Spalten neu anordnen möchten.

* Sie `project-away` können alle Spalten verwenden, die in der ursprünglichen Tabelle vorhanden sind oder als Teil der Abfrage berechnet wurden.


**Beispiele**

Die Eingabetabelle `T` umfasst drei Spalten vom Typ `long`: `A`, `B` und `C`.

```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|Ein|B|
|---|---|
|1|2|

Entfernen von Spalten, die mit 'a' beginnen.

```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

