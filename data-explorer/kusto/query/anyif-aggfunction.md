---
title: anyif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt anyif() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 813df821bad1b7e57315dad9bcd7b1387a2cd678
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030077"
---
# <a name="anyif-aggregation-function"></a>anyif() (Aggregationsfunktion)

Wählt willkürlich einen Datensatz für jede Gruppe in einem [zusammengefassten Operator](summarizeoperator.md) aus, für den das Prädikat wahr ist, und gibt den Wert eines Ausdrucks über jeden solchen Datensatz zurück.

**Syntax**

`summarize`Expr , *Prädikat* )' *Expr* `anyif` `(`

**Argumente**

* *Expr*: Ein Ausdruck über jeden Datensatz, der aus der Eingabe ausgewählt wurde, um zurückzukehren.
* *Prädikat*: Prädikat zur Angabe, welche Datensätze für die Auswertung in Betracht gezogen werden können.

**Rückgabe**

Die `anyif` Aggregationsfunktion gibt den Wert des Ausdrucks zurück, der für jeden der zufällig ausgewählten Datensätze aus jeder Gruppe des Summarize-Operators berechnet wurde. Es können nur Datensätze ausgewählt werden, für die *Predikat* true zurückgibt (wenn das Prädikat nicht true zurückgibt, wird ein NULL-Wert erzeugt).

**Anmerkungen**

Diese Funktion ist nützlich, wenn Sie einen Beispielwert von einer Spalte pro Wert des zusammengesetzten Gruppenschlüssels abrufen möchten, sofern ein Prädikat wahr ist.

Die Funktion versucht, einen Nicht-NULL/nicht leeren Wert zurückzugeben, wenn ein solcher Wert vorhanden ist.

**Beispiele**

Zeigen Sie zufälligen Kontinent, der eine Bevölkerung von 300 Millionen bis 600 Millionen hat:

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="Beeinen 1":::
