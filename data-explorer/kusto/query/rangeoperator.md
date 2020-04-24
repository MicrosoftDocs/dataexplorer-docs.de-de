---
title: Bereichsoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Bereichsoperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7ec559a23e28568ce7abd8365cdc502ad05a9b0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510607"
---
# <a name="range-operator"></a>range-Operator

Erzeugt eine einspaltige Tabelle mit Werten.

Beachten Sie, dass keine Pipeline-Eingabe vorhanden ist. 

**Syntax**

`range`*ColumnName* `from` `step` *start* `to` *Startstoppschritt* *step*

**Argumente**

* *columnName*: Der Name der einzelnen Spalte in der Ausgabetabelle.
* *start*: Der kleinste Wert in der Ausgabe.
* *stop*: Der höchste Wert, der in der Ausgabe generiert wird (oder eine Begrenzung auf den höchsten Wert, wenn *Schrittschritte* über diesen Wert).
* *Schritt*: Die Differenz zwischen zwei aufeinander folgenden Werten. 

Die Argumente müssen numerische Werte, Datums- oder TimeSpan-Werte sein. Sie können nicht auf die Spalten einer Tabelle verweisen. (Wenn Sie den Bereich basierend auf einer Eingabetabelle berechnen möchten, verwenden Sie die Bereichsfunktion, möglicherweise mit dem Operator mv-expand.) 

**Rückgabe**

Eine Tabelle mit einer einzelnen Spalte namens *columnName*, deren Werte *start*, *start* `+` *step*, ... bis und bis *zum Anhalten*.

**Beispiel**  

Eine Tabelle von Mitternacht der vergangenen sieben Tage. Die bin (floor)-Funktion reduziert jede Zeit auf den Anfang des Tages.

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

```kusto
range Steps from 1 to 8 step 3
```

Das nächste Beispiel `range` zeigt, wie der Operator verwendet werden kann, um eine kleine, ad-hoc-Dimensionstabelle zu erstellen, die dann verwendet wird, um Nullen einzuführen, bei denen die Quelldaten keine Werte haben.

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