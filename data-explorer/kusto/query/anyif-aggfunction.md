---
title: anyif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird anyif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 54431e2d088f60fa8ea2a56bffea9faa374faeda
ms.sourcegitcommit: aaada224e2f8824b51e167ddb6ff0bab92e5485f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84626668"
---
# <a name="anyif-aggregation-function"></a>anyif () (Aggregations Funktion)

Wählt beliebig einen Datensatz für jede Gruppe in einem Zusammenfassungs [Operator](summarizeoperator.md)aus, für den das Prädikat "true" ist. Die-Funktion gibt den Wert eines Ausdrucks über jeden dieser Datensätze zurück.

**Syntax**

`summarize``anyif` `(` *Expr*, *Prädikat*`)`

**Argumente**

* *Expr*: ein Ausdruck für jeden Datensatz, der aus der Eingabe ausgewählt wurde, die zurückgegeben werden soll.
* *Predicate*: Prädikat, um anzugeben, welche Datensätze für die Auswertung in Erwägung gezogen werden können.

**Rückgabe**

Die `anyif` Aggregations Funktion gibt den Wert des Ausdrucks zurück, der für jeden der nach dem Zufallsprinzip ausgewählten Datensätze aus jeder Gruppe des Zusammenfassungs Operators berechnet wird. Es können nur Datensätze ausgewählt werden, für die das *Prädikat* "true" zurückgibt. Wenn das Prädikat "true" nicht zurückgibt, wird ein NULL-Wert erzeugt.

**Anmerkungen**

Diese Funktion ist nützlich, wenn Sie einen Beispiel Wert einer Spalte pro Wert des Verbund Gruppen Schlüssels erhalten möchten, der einem Prädikat entspricht, das "true" ist.

Die Funktion versucht, einen nicht-NULL-Wert zurückzugeben, wenn ein solcher Wert vorhanden ist.

**Beispiele**

Zeigen Sie einen zufälligen Kontinent an, der über eine Auffüllung von 300 bis 600 Millionen verfügt.

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="Alle 1":::
