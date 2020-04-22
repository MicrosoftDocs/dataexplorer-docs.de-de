---
title: any() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt any() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2a0b2aed48c9c5aa9d5b99bdb6cab68375827d2c
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030202"
---
# <a name="any-aggregation-function"></a>any() (Aggregationsfunktion)

Wählt willkürlich einen Datensatz für jede Gruppe in einem [zusammengefassten Operator](summarizeoperator.md)aus und gibt den Wert eines oder mehrerer Ausdrücke über jeden solchen Datensatz zurück.

**Syntax**

`summarize``any` *Expr* `,` *Expr2* ( Expr [ Expr2 ...]) | `(` `*``)`

**Argumente**

* *Expr*: Ein Ausdruck über jeden Datensatz, der aus der Eingabe ausgewählt wurde, um zurückzukehren.
* *Expr2* .. *ExprN*: Zusätzliche Ausdrücke.

**Rückgabe**

Die `any` Aggregationsfunktion gibt die Werte der ausdrücken zurück, die für jeden Datensatz berechnet wurden und nach dem Zufallsprinzip aus jeder Gruppe des Summarize-Operators ausgewählt wurden.

Wenn `*` das Argument angegeben wird, verhält sich die Funktion so, als wären die Ausdrücke alle Spalten der Eingabe für den Summarize-Operator, der die Gruppierten-nach-Spalten, falls vorhanden, ausschließt.

**Anmerkungen**

Diese Funktion ist nützlich, wenn Sie einen Beispielwert von einer oder mehreren Spalten pro Wert des zusammengesetzten Gruppenschlüssels abrufen möchten.

Wenn der Funktion ein einzelner Spaltenverweis zur Verfügung gestellt wird, wird versucht, einen Wert ohne NULL/nicht leer zurückzugeben, wenn ein solcher Wert vorhanden ist.

Aufgrund der zufälligen Natur dieser Funktion ist die mehrfache Verwendung `summarize` in einer einzigen Anwendung des Operators nicht gleichbedeutend mit der Verwendung eines einzigen Mals mit mehreren Ausdrücken. Erstere können jede Anwendung einen anderen Datensatz auswählen lassen, während letztere garantiert, dass alle Werte über einen einzelnen Datensatz (pro unterschiedlicher Gruppe) berechnet werden.

**Beispiele**

Zufällige Kontinente anzeigen:

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="Beeinen 1":::

Zeigen Sie alle Details für einen zufälligen Datensatz an:

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="Alle 2":::

Zeigen Sie alle Details für jeden zufälligen Kontinent:

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="Alle 3":::
