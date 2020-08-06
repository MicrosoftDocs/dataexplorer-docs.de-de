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
ms.openlocfilehash: 6d40b0a95b71999382f5fc4e83f108717ac5d683
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803385"
---
# <a name="anyif-aggregation-function"></a>anyif () (Aggregations Funktion)

Wählt beliebig einen Datensatz für jede Gruppe in einem Zusammenfassungs [Operator](summarizeoperator.md)aus, für den das Prädikat "true" ist. Die-Funktion gibt den Wert eines Ausdrucks über jeden dieser Datensätze zurück.

> [!NOTE]
> Diese Funktion ist nützlich, wenn Sie einen Beispiel Wert von einer Spalte pro Wert des Verbund Gruppen Schlüssels erhalten möchten, wobei ein Prädikat mit dem Wert "true" gilt.
> Wenn ein solcher Wert vorhanden ist, versucht die Funktion, einen nicht-NULL-Wert zurückzugeben, der nicht leer ist.

## <a name="syntax"></a>Syntax

`summarize``anyif` `(` *Expr*, *Prädikat*`)`

## <a name="arguments"></a>Argumente

* *Expr*: ein Ausdruck für jeden Datensatz, der aus der Eingabe ausgewählt wurde, die zurückgegeben werden soll.
* *Predicate*: Prädikat, um anzugeben, welche Datensätze für die Auswertung in Erwägung gezogen werden können.

## <a name="returns"></a>Gibt zurück

Die `anyif` Aggregations Funktion gibt den Wert des Ausdrucks zurück, der für jeden der nach dem Zufallsprinzip ausgewählten Datensätze aus jeder Gruppe des Zusammenfassungs Operators berechnet wird. Es können nur Datensätze ausgewählt werden, für die das *Prädikat* "true" zurückgibt. Wenn das Prädikat "true" nicht zurückgibt, wird ein NULL-Wert erzeugt.

## <a name="examples"></a>Beispiele

Zeigen Sie einen zufälligen Kontinent an, der über eine Auffüllung von 300 bis 600 Millionen verfügt.

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="Alle 1":::
