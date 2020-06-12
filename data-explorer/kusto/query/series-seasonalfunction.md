---
title: series_seasonal ()-Azure Daten-Explorer
description: In diesem Artikel wird series_seasonal () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 054d4be758001609fbc3100a4a6c8698ef8f69f6
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717307"
---
# <a name="series_seasonal"></a>series_seasonal()

Berechnet die Saison Komponente einer Reihe entsprechend der erkannten oder gegebenen saisonalen Zeitspanne.

**Syntax**

`series_seasonal(`*Reihe* `[,` *Zeitraum*`])`

**Argumente**

* *Series*: Eingabe eines numerischen dynamischen Arrays
* *Period* (optional): ganzzahlige Anzahl von Containern in jedem saisonalen Zeitraum, mögliche Werte:
    *  -1 (Standard): erkennt den Zeitraum automatisch mithilfe von [series_periods_detect ()](series-periods-detectfunction.md) mit einem Schwellenwert von *0,7*. Gibt Nullen zurück, wenn Saisonalität nicht erkannt wird.
    * Positive ganze Zahl: wird als Zeitraum für die Saison Komponente verwendet.
    * Beliebiger anderer Wert: ignoriert Saisonalität und gibt eine Reihe von Nullen zurück.

**Rückgabe**

Dynamisches Array mit derselben Länge wie die *Reihen* Eingabe, die die berechnete Saison Komponente der Reihe enthält. Die Saison Komponente wird in den Zeiträume als *Median* aller Werte berechnet, die dem Speicherort der bin entsprechen.

## <a name="examples"></a>Beispiele

### <a name="auto-detect-the-period"></a>Den Zeitraum automatisch erkennen

Im folgenden Beispiel wird der Zeitraum der Reihe automatisch erkannt. Der Zeitraum der ersten Reihe wird als sechs und die zweiten fünf Behälter erkannt. Der Zeitraum der dritten Reihe ist zu kurz, um erkannt zu werden, und gibt eine Reihe von Nullen zurück. Weitere Informationen zum [Erzwingen des Zeitraums finden Sie](#force-a-period)im nächsten Beispiel.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2, 5, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1, 2, 3, 4, 3, 2, 1]|[1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0, 2.0, 3.0, 4.0, 3.0, 2.0, 1.0]|
|[8, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10, 10, 12, 14, 12, 10]|[10.0, 12.0, 14,0, 12.0, 10.0, 10.0, 12.0, 14,0, 12.0, 10.0, 10.0, 12.0, 14,0, 12.0, 10.0, 10.0, 12.0, 14,0, 12.0, 10.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0]|

### <a name="force-a-period"></a>Erzwingen eines Zeitraums

In diesem Beispiel ist der Zeitraum der Reihe zu kurz, um von [series_periods_detect ()](series-periods-detectfunction.md)erkannt zu werden. daher erzwingen wir explizit den Zeitraum, um das saisonale Muster zu erhalten.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1, 3, 5, 1, 3, 5, 2, 4, 6]|[1.0, 3.0, 5.0, 1.0, 3.0, 5.0, 1.0, 3.0, 5.0]|
|[1, 3, 5, 2, 4, 6, 1, 3, 5, 2, 4, 6]|[1.5, 3,5, 5.5, 1.5, 3,5, 5.5, 1.5, 3,5, 5.5, 1.5, 3.5, 5.5]|
 
## <a name="next-steps"></a>Nächste Schritte

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)
