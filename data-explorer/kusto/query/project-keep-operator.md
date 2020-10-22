---
title: 'Project-Keep-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Project-Keep-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/21/2020
ms.openlocfilehash: 2efc06ad701cc734ffad0bec7d89e3d3ef1d390d
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356717"
---
# <a name="project-keep-operator"></a>Project-Keep-Operator

Wählen Sie aus, welche Spalten aus der Eingabe in der Ausgabe aufbewahrt werden sollen.

```kusto
T | project-keep price, quantity, zz*
```

Die Reihenfolge der Spalten im Ergebnis wird durch ihre ursprüngliche Reihenfolge in der Tabelle bestimmt. Nur die Spalten, die als Argumente angegeben wurden, werden beibehalten. Die anderen Spalten werden aus dem Ergebnis ausgeschlossen. Siehe auch [`project`](projectoperator.md).

## <a name="syntax"></a>Syntax

*T* `| project-keep` *columnnameorpattern* [ `,` ...]

## <a name="arguments"></a>Argumente

* *T*: die Eingabe Tabelle
* *Columnnameorpattern:* Der Name des Spalten-oder Spalten Platzhalter Musters, das in der Ausgabe beibehalten werden soll.

## <a name="returns"></a>Rückgabe

Eine Tabelle mit Spalten, die als Argumente benannt wurden. Enthält die gleiche Anzahl von Zeilen wie die Eingabe Tabelle.

> [!TIP]
>* Verwenden Sie zum Umbenennen von Spalten [`project-rename`](projectrenameoperator.md) .
>* Verwenden Sie, um Spalten neu anzuordnen [`project-reorder`](projectreorderoperator.md) .
>* `project-keep`Alle Spalten, die in der ursprünglichen Tabelle vorhanden sind oder als Teil der Abfrage berechnet wurden, sind möglich.

## <a name="example"></a>Beispiel

Die Eingabetabelle `T` umfasst drei Spalten vom Typ `long`: `A`, `B` und `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A1:long, A2:long, B:long) [1, 2, 3]
| project-keep A*    // Keeps only columns A1 and A2 in the output
```

|A1|A2|
|---|---|
|1|2|

## <a name="see-also"></a>Weitere Informationen

Um auszuwählen, welche Spalten aus der Eingabe aus der Ausgabe ausgeschlossen werden sollen, verwenden Sie [Project-Away](projectawayoperator.md).
