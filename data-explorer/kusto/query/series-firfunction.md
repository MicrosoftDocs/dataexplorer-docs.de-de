---
title: series_fir() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird series_fir() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: bbdf93504be431d77c19a881cf79d1e1d3d400ac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663573"
---
# <a name="series_fir"></a>series_fir()

Wendet einen Finite Impulse Response-Filter auf eine Serie an.  

Nimmt einen Ausdruck, der dynamische summische Arrays enthält, als Eingabe und wendet einen [Finite Impulse Response-Filter](https://en.wikipedia.org/wiki/Finite_impulse_response) an. Durch die `filter` Angabe der Koeffizienten kann es für die Berechnung des gleitenden Durchschnitts, Glättung, Änderungserkennung und viele weitere Anwendungsfälle verwendet werden. Die Funktion verwendet die Spalte mit dem dynamischen Array und einem statischen, dynamischen Array der Filterkoeffizienten als Eingabe und wendet die Filter auf die Spalte an. Sie gibt eine neue dynamische Array-Spalte mit der gefilterten Ausgabe aus.  

**Syntax**

`series_fir(`*x* `,` *filter* Filter`,` [`,` *normalisieren*[ *Mitte*]]`)`

**Argumente**

* *x*: Dynamische Arrayzelle, die ein Array numerischer Werte ist, in der Regel die resultierende Ausgabe von [Make-Series-](make-seriesoperator.md) oder make_list-Operatoren. [make_list](makelist-aggfunction.md)
* *filter*: Ein konstanter Ausdruck, der die Koeffizienten des Filters enthält (gespeichert als dynamisches Array numerischer Werte).
* *normalisieren*: Optionaler boolescher Wert, der angibt, ob der Filter normalisiert werden soll (d. h. geteilt durch die Summe der Koeffizienten). Wenn *Filter* negative Werte enthält, muss `false` *normalize* als `null`angegeben werden, andernfalls wird das Ergebnis . Wenn nicht angegeben, wird ein Standardwert von *normalize* angenommen, abhängig vom Vorhandensein negativer Werte im *Filter:* `false`Wenn *Filter* mindestens einen negativen Wert enthält, wird als *normalisieren* angenommen.  
Die Normalisierung ist eine bequeme Möglichkeit, um sicherzustellen, dass die Summe der Koeffizienten 1 ist und der Filter die Serie daher nicht verstärkt oder dämpft. Beispielsweise könnte der gleitende Durchschnitt von 4 Abschnitten durch *Filter*=[1,1,1]und *normalisiert*=true angegeben werden, was einfacher ist als die Eingabe [0.25,0.25.0.0.25,0.25].
* *mitte*: Ein optionaler boolescher Wert, der angibt, ob der Filter symmetrisch auf ein Zeitfenster vor und nach dem aktuellen Punkt oder auf ein Zeitfenster vom aktuellen Punkt rückwärts angewendet wird. Standardmäßig ist „center“ auf „false“ festgelegt, was zum Szenario für das Streaming von Daten passt, wo wir den Filter nur auf aktuelle und ältere Punkte anwendet können. Allerdings können Sie den Wert bei der Ad-hoc-Verarbeitung auf „true“ festlegen, um diese mit der Zeitreihe zu synchronisieren (siehe folgende Beispiele). Technisch steuert dieser Parameter die [Gruppenverzögerung](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)des Filters .

**Beispiele**

* Die Berechnung eines gleitenden Durchschnitts von 5 Punkten kann durch Festlegen des *Filters*=[1,1,1,1] und *Normalisieren* = `true` (Standard) durchgeführt werden. Beachten Sie den Effekt von `true` *Center* = `false` (Standard) vs. :

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Diese Abfrage gibt Folgendes zurück:  
*5h_MovingAvg*: 5 Punkte gleitender Durchschnitt Filter. Die Spitze wird geglättet und um (5-1)/2 = 2 Stunden verschoben.  
*5h_MovingAvg_centered*: gleiche, aber mit Einstellung center=true, bewirkt, dass der Peak an seiner ursprünglichen Position bleibt.

:::image type="content" source="images/samples/series-fir.png" alt-text="Serie Tanne":::

* Die Berechnung der Differenz zwischen einem Punkt und seinem vorhergehenden Punkt kann durch Einstellen des *Filters*=[1,-1] durchgeführt werden:

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```
:::image type="content" source="images/samples/series-fir2.png" alt-text="Serie Tannen 2":::
