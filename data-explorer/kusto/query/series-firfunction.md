---
title: series_fir ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird series_fir () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 4641da6481365d13919de59708387b689581c9ce
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618796"
---
# <a name="series_fir"></a>series_fir()

Wendet einen endlichen Impuls Antwort Filter auf eine Reihe an.  

Nimmt einen Ausdruck, der das dynamische numerische Array enthält, als Eingabe und wendet einen [endlichen Impuls Antwort](https://en.wikipedia.org/wiki/Finite_impulse_response) Filter an. Durch die Angabe `filter` der Koeffizienten kann Sie zum Berechnen des gleitenden Durchschnitts, der Glättung, der Änderungs Erkennung und von vielen weiteren Anwendungsfällen verwendet werden. Die Funktion verwendet die Spalte mit dem dynamischen Array und einem statischen, dynamischen Array der Filterkoeffizienten als Eingabe und wendet die Filter auf die Spalte an. Sie gibt eine neue dynamische Array-Spalte mit der gefilterten Ausgabe aus.  

**Syntax**

`series_fir(`*x* `,` - *Filter* [`,` *normalisieren*[`,` *Center*]]`)`

**Argumente**

* *x*: dynamische Array Zelle, bei der es sich um ein Array numerischer Werte handelt, normalerweise die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren.
* *Filter*: ein konstanter Ausdruck, der die Koeffizienten des Filters (gespeichert als dynamisches Array numerischer Werte) enthält.
* *normalisieren*: Optionaler boolescher Wert, der angibt, ob der Filter normalisiert werden soll (d. h. dividiert durch die Summe der Koeffizienten). Wenn der *Filter* negative Werte enthält, muss *normalisieren* als `false`angegeben werden `null`. andernfalls ist das Ergebnis. Wenn der Wert nicht angegeben wird, wird der Standardwert *normalisieren* abhängig vom vorhanden sein negativer Werte im *Filter*angenommen: Wenn *Filter* mindestens einen negativen Wert enthält, wird als *normalisieren* angenommen `false`.  
Normalisierung ist eine bequeme Möglichkeit, um sicherzustellen, dass die Summe der Koeffizienten 1 ist, sodass der Filter die Reihe nicht verstärkt oder abgleicht. Beispielsweise könnte der gleitende Durchschnitt von 4 Containern durch *Filter*= [1, 1, 1, 1] und *normalisiert*= true angegeben werden. Dies ist einfacher als die Eingabe von [0,25, 0.25.0.25, 0,25].
* *Center*: ein optionaler boolescher Wert, der angibt, ob der Filter symmetrisch in einem Zeitfenster vor und nach dem aktuellen Punkt oder in einem Zeitfenster vom aktuellen Punkt aus angewendet wird. Standardmäßig ist „center“ auf „false“ festgelegt, was zum Szenario für das Streaming von Daten passt, wo wir den Filter nur auf aktuelle und ältere Punkte anwendet können. Allerdings können Sie den Wert bei der Ad-hoc-Verarbeitung auf „true“ festlegen, um diese mit der Zeitreihe zu synchronisieren (siehe folgende Beispiele). Technisch gesehen steuert dieser Parameter die [Gruppenverzögerung](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)des Filters.

**Beispiele**

* Das Berechnen eines gleitenden Durchschnitts von 5 Punkten kann durch Festlegen von *Filter*= [1, 1, 1, 1, 1] und *normalisieren* = `true` (Standard) erfolgen. Beachten Sie die Auswirkung von *Center* = `false` (Standard) im `true`Vergleich zu:

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Diese Abfrage gibt Folgendes zurück:  
*5h_MovingAvg*: 5 Punkte Filter für den gleitenden Durchschnitt. Die Spitze wird geglättet und um (5-1)/2 = 2 Stunden verschoben.  
*5h_MovingAvg_centered*: identisch, aber wenn Sie Center = true festlegen, verbleibt die Spitze an Ihrem ursprünglichen Speicherort.

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="Reihe FIR" border="false":::

* Das Berechnen der Differenz zwischen einem Punkt und dem vorhergehenden Punkt kann durch Festlegen von *Filter*= [1,-1] erfolgen:

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="Reihe FIR 2" border="false":::
