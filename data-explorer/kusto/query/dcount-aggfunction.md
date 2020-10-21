---
title: DCount () (Aggregations Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird DCount () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b35bb7944e894256056e03eb756ac85cf1354ba8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247705"
---
# <a name="dcount-aggregation-function"></a>DCount () (Aggregations Funktion)

Gibt eine Schätzung für die Anzahl der unterschiedlichen Werte zurück, die von einem skalaren Ausdruck in der Zusammenfassungs Gruppe entnommen werden.

> [!NOTE]
> Die `dcount()` Aggregations Funktion ist in erster Linie für das Einschätzen der Kardinalität großer Mengen nützlich. Die Leistung wird auf Genauigkeit gewartet, und es kann ein Ergebnis zurückgegeben werden, das sich zwischen den Ausführungen ändert. Die Reihenfolge der Eingaben hat möglicherweise Auswirkungen auf die Ausgabe.

## <a name="syntax"></a>Syntax

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>Argumente

* *Expr*: ein skalarer Ausdruck, dessen unterschiedliche Werte gezählt werden sollen.
* *Genauigkeit*: ein optionales `int` Literalwert, der die angeforderte Schätzgenauigkeit definiert. Unterstützte Werte finden Sie unten. Wenn nicht angegeben, wird der Standardwert `1` verwendet.

## <a name="returns"></a>Rückgabe

Gibt eine Schätzung der Anzahl der unterschiedlichen Werte von *`Expr`* in der Gruppe zurück.

## <a name="example"></a>Beispiel

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D-Anzahl":::

Eine genaue Anzahl von unterschiedlichen Werten von, `V` gruppiert nach `G` .

```kusto
T | summarize by V, G | summarize count() by G
```

Diese Berechnung erfordert sehr viel internen Arbeitsspeicher, da unterschiedliche Werte von `V` mit der Anzahl der unterschiedlichen Werte von multipliziert werden `G` .
Dies kann zu Speicherfehlern oder zu großen Ausführungszeiten führen. 
`dcount()`bietet eine schnelle und zuverlässige Alternative:

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>Schätzgenauigkeit

Die `dcount()` Aggregatfunktion verwendet eine Variante des [hyperloglog (HLL)-Algorithmus](https://en.wikipedia.org/wiki/HyperLogLog), der eine stochastischen-Schätzung der Set-Kardinalität vornimmt. Der Algorithmus stellt einen "Knopf" bereit, mit dem Genauigkeit und Ausführungszeit pro Arbeitsspeicher ausgeglichen werden können:

|Genauigkeit|Fehler (%)|Anzahl der Einträge   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> Die Spalte "Entry count" ist die Anzahl von 1-Byte-Leistungsindikatoren in der HLL-Implementierung.

Der Algorithmus enthält einige Vorkehrungen für eine perfekte Anzahl (null-Fehler), wenn die festgelegte Kardinalität klein genug ist:
* Wenn die Genauigkeits Stufe ist `1` , werden 1000-Werte zurückgegeben.
* Wenn die Genauigkeits Stufe ist `2` , werden 8000-Werte zurückgegeben.

Die Fehler Bindung ist probabilistisch und keine theoretische Grenze. Der Wert ist die Standardabweichung der Fehlerverteilung (Sigma), und 99,7% der Schätzungen weisen einen relativen Fehler von unter 3 x Sigma auf.

Die folgende Abbildung zeigt die Wahrscheinlichkeitsverteilungsfunktion des relativen Schätz Fehlers in Prozent für alle unterstützten Genauigkeits Einstellungen:

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="D-Anzahl":::
