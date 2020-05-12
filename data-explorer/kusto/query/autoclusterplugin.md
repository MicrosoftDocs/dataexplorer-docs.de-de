---
title: 'Autocluster-Plug-in: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt das Autocluster-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b2d76c0dd7a3ee0724db67aa8cb0cd9229748fa
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225529"
---
# <a name="autocluster-plugin"></a>Autocluster-Plugin

```kusto
T | evaluate autocluster()
```

Mit AutoCluster werden häufige Muster von diskreten Attributen (Dimensionen) in den Daten ermittelt, und die Ergebnisse der ursprünglichen Abfrage (ob mit 100 oder 100.000 Zeilen) werden auf eine kleine Anzahl von Mustern reduziert. AutoCluster wurde als Hilfe beim Analysieren von Fehlern (z.B. Ausnahmen, Abstürze) entwickelt, kann aber für jedes gefilterte Dataset verwendet werden. 

**Syntax**

`T | evaluate autocluster(`*Argumente*`)`

**Rückgabe**

AutoCluster gibt eine (in der Regel kleine) Gruppe von Mustern zurück, in der Teile der Daten mit gemeinsamen Werten über mehrere diskrete Attribute hinweg erfasst werden. Jede Zeile in den Ergebnissen steht für ein Muster. 

Die erste Spalte ist die Segment-ID. Die nächsten beiden Spalten sind die Anzahl und der Prozentsatz der Zeilen aus der ursprünglichen Abfrage, die vom Muster aufgezeichnet werden. Die restlichen Spalten stammen aus der ursprünglichen Abfrage. Sie enthalten als Wert entweder einen bestimmten Wert aus der Spalte oder einen Platzhalterwert (standardmäßig NULL) – also variable Werte. 

Beachten Sie, dass die Muster nicht unzusammenhängend sind: sie können sich überlappen und decken normalerweise nicht alle ursprünglichen Zeilen ab. Einige Zeilen fallen ggf. nicht in eines der Muster.

> [!TIP]
> Verwenden Sie [Where](./whereoperator.md) und [Project](./projectoperator.md) in der eingabepipe, um die Daten auf die für Sie interessanten Daten zu reduzieren.
>
> Wenn Sie eine interessante Zeile finden, können Sie dafür einen Drilldown durchführen, indem Sie die jeweiligen Werte dem `where` -Filter hinzufügen.

**Argumente (alle optional)**

`T | evaluate autocluster(`[*Sizeweight*, *weightcolumn*, *numseeds*, *customwildcard*, *customwildcard*,...]`)`

Alle Argumente sind optional, aber sie müssen wie oben angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden sollte, können Sie eine Tilde („~“) angeben (siehe Beispiele unten).

Verfügbare Argumente:

* Sizeweight-0 < *Double* < 1 [Standard: 0,5]

    Hiermit erhalten Sie Kontrolle über die Balance zwischen der „generischen“ (hohe Abdeckung) und „informativen“ (viele gemeinsame Werte) Vorgehensweise. Wenn Sie den Wert erhöhen, wird die Anzahl von Mustern normalerweise reduziert, und jedes Muster deckt einen größeren Prozentsatz ab. Wenn Sie den Wert verringern, werden normalerweise mehr spezifische Muster mit mehr gemeinsamen Werten und einer geringeren prozentualen Abdeckung produziert. Die Formel im Hintergrund ist ein gewichteter geometrischer Mittelwert zwischen der normalisierten generischen Bewertung und der informativen Bewertung mit `SizeWeight` und als Gewichtungen `1-SizeWeight` . 

    Ein Beispiel: `T | evaluate autocluster(0.8)`

* WeightColumn - *column_name*

    Berücksichtigt jede Zeile in der Eingabe gemäß dem angegebenen Gewicht (standardmäßig verfügt jede Spalte über eine Gewichtung von „1“). Das Argument muss ein Name einer numerischen Spalte sein (z.B. int, long, real). Eine übliche Nutzung einer Gewichtungsspalte besteht darin, die Stichprobenerstellung oder die Bucket-Zuordnung/Aggregation der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.
    
    Ein Beispiel: `T | evaluate autocluster('~', sample_Count)` 

* Numseeds- *int* [Standardwert: 25] 

    Die Anzahl von Startwerten bestimmt die Anzahl von anfänglichen lokalen Suchpunkten des Algorithmus. Je nach der Struktur der Daten erhöht sich beim Erhöhen der Anzahl von Startwerten in einigen Fällen die Anzahl (bzw. Qualität) von Ergebnissen, da ein größerer Suchbereich vorhanden ist. Gleichzeitig verlangsamen sich die Abfragen. Der Wert führt zu einer Verringerung der Ergebnisse in beiden Richtungen, sodass der absteigende Wert unter 5 zu vernachlässigenden Leistungsverbesserungen führt und die Erhöhung über 50 selten zusätzliche Muster generiert.

    Beispiel: `T | evaluate autocluster('~', '~', 15)`

* Customwildcard- *"any_value_per_type"*

    Legt den Platzhalterwert für einen bestimmten Typ in der Ergebnistabelle fest, der angibt, dass das aktuelle Muster keine Einschränkung für diese Spalte besitzt.
    Der Standard ist „null“, da der Standard eine leere Zeichenfolge ist. Wenn der Standardwert ein in den Daten funktionierender Wert ist, sollte ein anderer Platzhalter Wert verwendet werden (z. b. `*` ).

    Ein Beispiel: `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|SegmentId|Anzahl|Percent|Bundesland/Kanton|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7||Hagel|Nein
|1|512|8,7||Sturm|YES
|2|898|15,3|TEXAS||

**Beispiel mit benutzerdefinierten Platzhaltern**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|SegmentId|Anzahl|Percent|Bundesland/Kanton|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7|\*|Hagel|Nein
|1|512|8,7|\*|Sturm|YES
|2|898|15,3|TEXAS|\*|\*

**Siehe auch**

* [basket](./basketplugin.md)
* [Ver](./reduceoperator.md)

**Zusätzliche Informationen**

* AutoCluster basiert zum größten Teil auf dem Seed-Expand-Algorithmus des folgenden Dokuments: [Algorithmen für Data Mining von Telemetriedaten mit diskreten Attributen](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1). 

