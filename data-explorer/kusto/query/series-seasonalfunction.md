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
ms.openlocfilehash: 88160a55ba8342e3ed6bce90ec77c5a370ab7358
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372471"
---
# <a name="series_seasonal"></a>series_seasonal()

Berechnet die Saison Komponente einer Reihe entsprechend der erkannten oder angegebenen saisonalen Zeitspanne.

**Syntax**

`series_seasonal(`*Reihe* `[,` *Zeitraum*`])`

**Argumente**

* *Series*: Eingabe eines numerischen dynamischen Arrays
* *Period* (optional): ganzzahlige Anzahl von Containern in jedem saisonalen Zeitraum, mögliche Werte:
    *  -1 (Standard): Automatisches Erkennen des Zeitraums mithilfe von [series_periods_detect ()](series-periods-detectfunction.md) mit einem Schwellenwert von *0,7*. gibt Nullen zurück, wenn Saisonalität nicht erkannt wird.
    * positive ganze Zahl: wird als Zeitraum für die Saison Komponente verwendet.
    * beliebiger anderer Wert: Saisonalität ignorieren und eine Reihe von Nullen zurückgeben

**Rückgabe**

Dynamisches Array mit derselben Länge wie die *Reihen* Eingabe, die die berechnete Saison Komponente der Reihe enthält. Die Saison Komponente wird als Mittelwert aller Werte berechnet *, die dem* Speicherort des bin über die Zeiträume entsprechen.

**Siehe auch:**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**Beispiele**

**1. erkennen Sie den Zeitraum automatisch.**

Im folgenden Beispiel wird der Zeitraum der Reihe automatisch erkannt, der Zeitraum der ersten Reihe als 6 Container und die zweiten fünf Behälter, der Zeitraum der dritten Reihe ist zu kurz, um erkannt zu werden, und gibt eine Reihe von Nullen zurück (siehe das nächste Beispiel, wie Sie den Zeitraum erzwingen).

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



**2. Erzwingen eines Zeitraums**

Im folgenden Beispiel ist der Zeitraum der Reihe zu kurz, um von [series_periods_detect ()](series-periods-detectfunction.md) erkannt zu werden. daher erzwingen wir den Zeitraum explizit, um das saisonale Muster zu erhalten.

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
