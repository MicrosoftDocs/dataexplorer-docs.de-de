---
title: series_fir ()-Azure Daten-Explorer
description: In diesem Artikel wird series_fir () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 8085455616fc97337ca115c1ef5b0c0e2422e08e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246176"
---
# <a name="series_fir"></a>series_fir()

Wendet einen FIR-Filter (Finite Impulse Response) auf eine Reihe an.  

Die Funktion nimmt einen Ausdruck, der ein dynamisches numerisches Array enthält, als Eingabe an und wendet einen [endlichen Impuls Antwort](https://en.wikipedia.org/wiki/Finite_impulse_response) Filter an. Durch die Angabe der Koeffizienten `filter` kann Sie zum Berechnen eines gleitenden Durchschnitts, Glättung, Änderungs Erkennung und vielen weiteren Anwendungsfällen verwendet werden. Die-Funktion übernimmt die-Spalte mit dem dynamischen Array und ein statisches dynamisches Array der Filterkoeffizienten als Eingabe und wendet den Filter auf die Spalte an. Sie gibt eine neue dynamische Array-Spalte mit der gefilterten Ausgabe aus.  

## <a name="syntax"></a>Syntax

`series_fir(`*x* - `,` *Filter* [ `,` *normalisieren*[ `,` *Center*]]`)`

## <a name="arguments"></a>Argumente

* *x*: dynamische Array Zelle numerischer Werte. In der Regel die resultierende Ausgabe von [make-Series-](make-seriesoperator.md) oder [make_list](makelist-aggfunction.md) -Operatoren.
* *Filter*: ein konstanter Ausdruck, der die Koeffizienten des Filters (gespeichert als dynamisches Array numerischer Werte) enthält.
* *normalisieren*: Optionaler boolescher Wert, der angibt, ob der Filter normalisiert werden soll. Das heißt, dividiert durch die Summe der Koeffizienten. Wenn der Filter negative Werte enthält, muss *normalisieren* als angegeben werden `false` . andernfalls ist das Ergebnis `null` . Wenn kein Wert angegeben wird, wird der Standardwert *normalisieren* angenommen, abhängig davon, ob negative Werte im *Filter*vorhanden sind. Wenn der *Filter* mindestens einen negativen Wert enthält, wird bei *normalisieren* angenommen `false` .  
Normalisierung ist eine bequeme Möglichkeit, um sicherzustellen, dass die Summe der Koeffizienten 1 ist. Der Filter wird die Reihe nicht verstärken oder dämpfen. Beispielsweise könnte der gleitende Durchschnitt von vier Containern durch *Filter*= [1, 1, 1, 1] und *normalisiert*= true angegeben werden. Dies ist einfacher als die Eingabe von [0,25, 0.25.0.25, 0,25].
* *Center*: ein optionaler boolescher Wert, der angibt, ob der Filter symmetrisch in einem Zeitfenster vor und nach dem aktuellen Punkt oder in einem Zeitfenster vom aktuellen Punkt aus angewendet wird. Standardmäßig ist "Center" auf "false" fest, was dem Szenario für das Streaming von Daten entspricht, bei dem der Filter nur auf die aktuellen und die älteren Punkte angewendet werden kann. Bei der Ad-hoc-Verarbeitung können Sie Sie jedoch auf festlegen `true` , sodass Sie mit der Zeitreihe synchronisiert bleibt. Siehe folgende Beispiele. Dieser Parameter steuert die [Gruppenverzögerung](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)des Filters.

## <a name="examples"></a>Beispiele

* Berechnen Sie einen gleitenden Durchschnitt von fünf Punkten, indem Sie *Filter*= [1, 1, 1, 1, 1] und *normalisieren* = `true` (Standard) festlegen. Beachten Sie die Auswirkung von *Center* = `false` (Standard) im Vergleich zu `true` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Diese Abfrage gibt Folgendes zurück:  
*5h_MovingAvg*: fünf Punkte gleitenden Durchschnitt Filter. Die Spitze wird geglättet und um (5-1)/2 = 2 Stunden verschoben.  
*5h_MovingAvg_centered*: identisch, aber durch Festlegen von `center=true` bleibt die Spitze am ursprünglichen Speicherort.

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="Reihe FIR" border="false":::

* Um den Unterschied zwischen einem Punkt und dem vorangehenden Punkt zu berechnen, legen Sie *Filter*= [1,-1] fest.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="Reihe FIR" border="false":::
