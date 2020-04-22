---
title: series_seasonal() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_seasonal() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 559731ab7dca2e49e124a856ca7a66a912583b62
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663128"
---
# <a name="series_seasonal"></a>series_seasonal()

Berechnet die saisonale Komponente einer Reihe entsprechend der ermittelten oder angegebenen Saisonperiode.

**Syntax**

`series_seasonal(`*Serienzeitraum* `[,` *period*`])`

**Argumente**

* *Serie*: Eingabe numerisches dynamisches Array
* *Zeitraum* (optional): Ganzzahl anzahl der Lagerplätze in jeder Saisonperiode, mögliche Werte:
    *  -1 (Standard): Automatische Erkennung des Zeitraums mit [series_periods_detect()](series-periods-detectfunction.md) mit einem Schwellenwert von *0,7*, gibt Nullen zurück, wenn die Saisonalität nicht erkannt wird
    * positive ganzzahlige: wird als Periode für die saisonale Komponente verwendet
    * jeder andere Wert: Saisonale Abhängigkeit ignorieren und eine Reihe von Nullen zurückgeben

**Rückgabe**

Dynamisches Array mit der gleichen Länge wie der *Reiheneingang,* der die berechnete saisonale Komponente der Serie enthält. Die saisonale Komponente wird als *Median* aller Werte berechnet, die der Position des Lagerplatzes über die Perioden hinweg entsprechen.

**Siehe auch:**

* [series_periods_detect()](series-periods-detectfunction.md)
* [series_periods_validate()](series-periods-validatefunction.md)

**Beispiele**

**1. Automatische Erkennung des Zeitraums**

Im folgenden Beispiel wird der Zeitraum der Serie automatisch erkannt, die Periode der ersten Reihe wird als 6 Abschnitte und die zweite 5 Abschnitte erkannt, die Dritte Reihe ist zu kurz, um erkannt zu werden, und gibt eine Reihe von Nullen zurück (siehe nächstes Beispiel zum Erzwingen der Periode).

```kusto
print s=dynamic([2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1])
| union (print s=dynamic([8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]))
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s)
```

|s|s_seasonal|
|---|---|
|[2,5,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1,2,3,4,3,2,1]|[1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0,2.0,3.0,4.0,3.0,2.0,1.0]|
|[8,12,14,12,10,10,12,14,12,10,10,12,14,12,10,10,12,14,12,10]|[10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0,10.0,12.0,14.0,12.0,10.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0,0.0]|



**2. Erzwingen eines Zeitraums**

Im folgenden Beispiel ist der Zeitraum der Serie zu kurz, um von [series_periods_detect()](series-periods-detectfunction.md) erkannt zu werden, so dass wir den Zeitraum explizit erzwingen, um das saisonale Muster zu erhalten.

```kusto
print s=dynamic([1,3,5,1,3,5,2,4,6]) 
| union (print s=dynamic([1,3,5,2,4,6,1,3,5,2,4,6]))
| extend s_seasonal = series_seasonal(s,3)
```

|s|s_seasonal|
|---|---|
|[1,3,5,1,3,5,2,4,6]|[1.0,3.0,5.0,1.0,3.0,5.0,1.0,3.0,5.0]|
|[1,3,5,2,4,6,1,3,5,2,4,6]|[1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5,1.5,3.5,5.5]|
