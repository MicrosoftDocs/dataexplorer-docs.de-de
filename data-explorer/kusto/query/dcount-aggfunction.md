---
title: dcount() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt dcount() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f1df8c93d21b73be3753468708a119177d4d602
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030406"
---
# <a name="dcount-aggregation-function"></a>dcount() (Aggregationsfunktion)

Gibt eine Schätzung für die Anzahl der unterschiedlichen Werte zurück, die von einem skalaren Ausdruck in der Zusammenfassungsgruppe genommen werden.

**Syntax**

... `|` `,` *Accuracy*`)` *Expr* Expr [ Genauigkeit ] ... `summarize` `dcount` `(`

**Argumente**

* *Expr*: Ein skalarer Ausdruck, dessen unterschiedliche Werte gezählt werden sollen.
* *Genauigkeit*: `int` Ein optionales Literal, das die angeforderte Schätzgenauigkeit definiert. Siehe unten für unterstützte Werte. Wenn nicht angegeben, `1` wird der Standardwert verwendet.

**Rückgabe**

Gibt eine Schätzung der Anzahl von unterschiedlichen Werten für *Expr* in der Gruppe zurück.

**Beispiel**

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D-Zählung":::

**Hinweise**

Die `dcount()` Aggregationsfunktion ist in erster Linie nützlich, um die Kardinalität großer Sets zu schätzen. Es handelt Leistung für Genauigkeit, und kann daher ein Ergebnis zurückgeben, das zwischen Ausführungen variiert (Reihenfolge der Inputs kann einen Einfluss auf seine Ausgabe haben).

So erhalten Sie eine genaue `V` Anzahl `G`unterschiedlicher Werte von gruppiert nach:

```kusto
T | summarize by V, G | summarize count() by G
```

Diese Berechnung erfordert viel internen Speicher, da unterschiedliche Werte von `V` `G`mit der Anzahl der unterschiedlichen Werte von multipliziert werden; Daher kann es zu Speicherfehlern oder großen Ausführungszeiten führen. `dcount()`bietet eine schnelle und zuverlässige Alternative:

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>Schätzgenauigkeit

Die `dcount()` Aggregatfunktion verwendet eine Variante des [HyperLogLog (HLL)-Algorithmus](https://en.wikipedia.org/wiki/HyperLogLog), der eine stochastische Schätzung der festgelegten Kardinalität ausführt. Der Algorithmus bietet einen "Knopf", der verwendet werden kann, um Genauigkeit und Ausführungszeit / Speichergröße auszugleichen:

|Genauigkeit|Fehler (%)|Eintragsanzahl   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0,8|2<sup>14</sup>|
|       2|      0,4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> Die Spalte "Eintragsanzahl" ist die Anzahl der 1-Byte-Zähler in der HLL-Implementierung.

Der Algorithmus enthält einige Bestimmungen für eine perfekte Anzahl (Nullfehler), wenn die eingestellte Kardinalität `1`klein genug ist (1000 `2`Werte, wenn die Genauigkeitsstufe ist, und 8000 Werte, wenn die Genauigkeitsstufe ist ).

Die fehlergebundene ist probabilistisch, keine theoretische Grenze. Der Wert ist die Standardabweichung der Fehlerverteilung (das Sigma), und 99,7% der Schätzungen haben einen relativen Fehler von unter 3 mal Sigma.

Im Folgenden wird die Wahrscheinlichkeitsverteilungsfunktion des relativen Schätzfehlers (in Prozent) für alle unterstützten Genauigkeitseinstellungen dargestellt:

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll-Fehlerverteilung":::
