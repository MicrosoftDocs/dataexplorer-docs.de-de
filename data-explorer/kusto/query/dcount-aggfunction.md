---
title: 'dcount() (Aggregationsfunktion): Azure Data Explorer'
description: In diesem Artikel wird die Aggregationsfunktion dcount() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 947ab0af6a5aaa98bb07b08005b940fdf2ce6ae5
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513181"
---
# <a name="dcount-aggregation-function"></a>dcount() (Aggregationsfunktion)

Gibt eine Schätzung für die Anzahl der unterschiedlichen Werte zurück, die von einem Skalarausdruck in der Zusammenfassungsgruppe übernommen werden.

> [!NOTE]
> Die `dcount()`-Aggregationsfunktion ist hauptsächlich zum Schätzen der Kardinalität großer Sätze nützlich. Sie tauscht Leistung gegen Genauigkeit und kann ein Ergebnis liefern, das von Ausführung zu Ausführung variiert. Die Reihenfolge der Eingaben hat möglicherweise Auswirkungen auf die Ausgabe.

## <a name="syntax"></a>Syntax

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>Argumente

* *Expr*: Ein Skalarausdruck, dessen unterschiedliche Werte gezählt werden sollen.
* *Genauigkeit*: Ein optionales `int`-Literal, das die angeforderte Schätzgenauigkeit definiert. Unterstützte Werte finden Sie weiter unten. Wenn nichts angegeben wird, wird der Standardwert `1` verwendet.

## <a name="returns"></a>Gibt zurück

Gibt eine Schätzung der Anzahl von unterschiedlichen Werten für *`Expr`* in der Gruppe zurück.

## <a name="example"></a>Beispiel

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D count":::

Ruft die genaue Anzahl der unterschiedlichen Werte von `V` gruppiert nach `G` ab.

```kusto
T | summarize by V, G | summarize count() by G
```

Diese Berechnung erfordert sehr viel internen Arbeitsspeicher, da unterschiedliche Werte von `V` mit der Anzahl der unterschiedlichen Werte von `G` multipliziert werden.
Dies kann zu Speicherfehlern oder langen Ausführungszeiten führen. 
`dcount()` bietet eine schnelle und zuverlässige Alternative:

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>Schätzgenauigkeit

Die `dcount()`-Aggregatfunktion verwendet eine Variante des [HLL-Algorithmus (HyperLogLog)](https://en.wikipedia.org/wiki/HyperLogLog), der eine stochaistische Schätzung der festgelegten Kardinalität vornimmt. Der Algorithmus stellt einen „Knopf“ bereit, über den Genauigkeit und Ausführungszeit pro Arbeitsspeichergröße ausgeglichen werden können:

|Genauigkeit|Fehler (%)|Entry count   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0,8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> Die Spalte „entry count“ ist die Anzahl von 1-Byte-Leistungsindikatoren in der HLL-Implementierung.

Der Algorithmus enthält einige Vorkehrungen für eine perfekte Anzahl (null Fehler), wenn die festgelegte Kardinalität klein genug ist:
* Wenn die Genauigkeitsgrad `1` ist, werden 1.000 Werte zurückgegeben.
* Wenn die Genauigkeitsgrad `2` ist, werden 8.000 Werte zurückgegeben.

Die Fehlerbindung ist probabilistisch und keine theoretische Grenze. Der Wert ist die Standardabweichung der Fehlerverteilung (Sigma), und 99,7 % der Schätzungen weisen einen relativen Fehler von unter 3 x Sigma auf.

Die folgende Abbildung zeigt die Wahrscheinlichkeitsverteilungsfunktion des relativen Schätzfehlers in Prozent für alle unterstützten Genauigkeitseinstellungen:

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="HLL-Fehlerverteilung":::
