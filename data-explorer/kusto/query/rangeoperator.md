---
title: 'Bereichs Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Bereichs Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1eddf7fe1e9121a134e222f6a19b9ebeede3a762
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243136"
---
# <a name="range-operator"></a>range-Operator

Erzeugt eine einspaltige Tabelle mit Werten.

Beachten Sie, dass keine Pipeline-Eingabe vorhanden ist. 

## <a name="syntax"></a>Syntax

`range`*ColumnName* `from` *starten* `to` Sie wird *beendet* `step` *Schritt*

## <a name="arguments"></a>Argumente

* *ColumnName*: der Name der einzelnen Spalte in der Ausgabe Tabelle.
* *Start*: der kleinste Wert in der Ausgabe.
* *stoppt*: der höchste Wert, der in der Ausgabe generiert wird (oder ein gebunden am höchsten Wert, wenn *Schritt* Schritte über diesem Wert).
* *Step*: der Unterschied zwischen zwei aufeinander folgenden Werten. 

Die Argumente müssen numerische Werte, Datums- oder TimeSpan-Werte sein. Sie können nicht auf die Spalten einer Tabelle verweisen. (Wenn Sie den Bereich basierend auf einer Eingabe Tabelle berechnen möchten, verwenden Sie die Range-Funktion, vielleicht mit dem MV-Expand-Operator.) 

## <a name="returns"></a>Rückgabe

Eine Tabelle mit einer einzelnen Spalte namens *ColumnName*, deren Werte *Start*, *Start* `+` *Schritt*,... bis zu und bis zum *Ende*.

## <a name="example"></a>Beispiel  

Eine Tabelle von Mitternacht der vergangenen sieben Tage. Die bin (floor)-Funktion reduziert jede Zeit auf den Anfang des Tages.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range LastWeek from ago(7d) to now() step 1d
```

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|


Eine Tabelle mit einer einzelnen Spalte namens `Steps` vom Typ `long`, deren Werte `1`, `4` und `7` sind.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range Steps from 1 to 8 step 3
```

Im nächsten Beispiel wird gezeigt, wie der- `range` Operator verwendet werden kann, um eine kleine Ad-hoc-Dimensions Tabelle zu erstellen, die dann verwendet wird, um Nullen einzuführen, bei denen die Quelldaten keine Werte aufweisen.

```kusto
range TIMESTAMP from ago(4h) to now() step 1m
| join kind=fullouter
  (Traces
      | where TIMESTAMP > ago(4h)
      | summarize Count=count() by bin(TIMESTAMP, 1m)
  ) on TIMESTAMP
| project Count=iff(isnull(Count), 0, Count), TIMESTAMP
| render timechart  
```
