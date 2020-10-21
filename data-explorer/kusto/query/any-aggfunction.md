---
title: 'Any () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird jede () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4d718cfb46e3a404c943d579feaf4733499ab3e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248230"
---
# <a name="any-aggregation-function"></a>Any () (Aggregations Funktion)

Wählt beliebig einen Datensatz für jede Gruppe in einem Zusammenfassungs [Operator](summarizeoperator.md)aus und gibt den Wert von einem oder mehreren Ausdrücken für jeden dieser Datensätze zurück.

## <a name="syntax"></a>Syntax

`summarize``any` `(` (*Expr* [ `,` *expr2* ...]) | `*``)`

## <a name="arguments"></a>Argumente

* *Expr*: ein Ausdruck für jeden Datensatz, der aus der Eingabe ausgewählt wurde, die zurückgegeben werden soll.
* *Expr2* .. *ExprN*: zusätzliche Ausdrücke.

## <a name="returns"></a>Rückgabe

Die `any` Aggregations Funktion gibt die Werte der Ausdrücke zurück, die für die einzelnen Datensätze berechnet werden, die nach dem Zufallsprinzip aus jeder Gruppe des Zusammenfassungs Operators ausgewählt werden.

Wenn das- `*` Argument bereitgestellt wird, verhält sich die-Funktion so, als ob es sich bei den Ausdrücken um alle Spalten der Eingabe des Zusammenfassungs Operators handelt, sofern vorhanden.

**Anmerkungen**

Diese Funktion ist nützlich, wenn Sie einen Beispiel Wert von einer oder mehreren Spalten pro Wert des Verbund Gruppen Schlüssels erhalten möchten.

Wenn die Funktion mit einem einzelnen Spalten Verweis bereitgestellt wird, wird versucht, einen nicht-NULL-Wert zurückzugeben, wenn ein solcher Wert vorhanden ist.

Aufgrund der zufälligen Natur dieser Funktion ist die mehrfache Verwendung in einer einzelnen Anwendung des `summarize` Operators nicht gleichbedeutend mit der Verwendung einer einzelnen Zeit mit mehreren Ausdrücken. Der erste kann für jede Anwendung einen anderen Datensatz auswählen, während letztere sicherstellt, dass alle Werte über einen einzelnen Datensatz (pro eindeutiger Gruppe) berechnet werden.

## <a name="examples"></a>Beispiele

Zufälligen Kontinent anzeigen:

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="Alle 1":::

Alle Details für einen zufälligen Datensatz anzeigen:

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="Alle 1":::

Alle Details für jeden zufälligen Kontinent anzeigen:

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="Alle 1":::
