---
title: 'Any () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird jede () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 73c3a660dc7a34f1f9fef840b13f47c13b4d1b2f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349736"
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

:::image type="content" source="images/aggfunction/any2.png" alt-text="Beliebige 2":::

Alle Details für jeden zufälligen Kontinent anzeigen:

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="Alle 3":::
