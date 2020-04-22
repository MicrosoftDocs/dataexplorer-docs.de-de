---
title: Autocluster-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Autocluster-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bbc89f8214b5d64cdc605d1771da7c27064cc629
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663903"
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

Die erste Spalte ist die Segment-ID. Die nächsten beiden Spalten sind die Anzahl und der Prozentsatz der Zeilen aus der ursprünglichen Abfrage, die vom Muster erfasst werden. Die restlichen Spalten stammen aus der ursprünglichen Abfrage. Sie enthalten als Wert entweder einen bestimmten Wert aus der Spalte oder einen Platzhalterwert (standardmäßig NULL) – also variable Werte. 

Beachten Sie, dass die Muster nicht unzusammenhängend sind: sie können sich überlappen und decken normalerweise nicht alle ursprünglichen Zeilen ab. Einige Zeilen fallen ggf. nicht in eines der Muster.

> [!TIP]
> Verwenden Sie [wo](./whereoperator.md) und [projizieren](./projectoperator.md) Sie in der Eingabepipe, um die Daten auf das zu reduzieren, was Sie interessieren.
>
> Wenn Sie eine interessante Zeile finden, können Sie dafür einen Drilldown durchführen, indem Sie die jeweiligen Werte dem `where` -Filter hinzufügen.

**Argumente (alle optional)**

`T | evaluate autocluster(`[*SizeWeight*, *WeightColumn*, *NumSeeds*, *CustomWildcard*, *CustomWildcard*, ...]`)`

Alle Argumente sind optional, aber sie müssen wie oben angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden sollte, können Sie eine Tilde („~“) angeben (siehe Beispiele unten).

Verfügbare Argumente:

* SizeWeight - 0 < *Double* < 1 [Standard: 0.5]

    Hiermit erhalten Sie Kontrolle über die Balance zwischen der „generischen“ (hohe Abdeckung) und „informativen“ (viele gemeinsame Werte) Vorgehensweise. Wenn Sie den Wert erhöhen, wird die Anzahl von Mustern normalerweise reduziert, und jedes Muster deckt einen größeren Prozentsatz ab. Wenn Sie den Wert verringern, werden normalerweise mehr spezifische Muster mit mehr gemeinsamen Werten und einer geringeren prozentualen Abdeckung produziert. Die unter der Haube Formel ist ein gewichteter geometrischer `SizeWeight` Mittelwert zwischen der normalisierten generischen Punktzahl und informativen Punktzahl mit und `1-SizeWeight` als Gewichte. 

    Beispiel: `T | evaluate autocluster(0.8)`

* WeightColumn - *column_name*

    Berücksichtigt jede Zeile in der Eingabe gemäß dem angegebenen Gewicht (standardmäßig verfügt jede Spalte über eine Gewichtung von „1“). Das Argument muss ein Name einer numerischen Spalte sein (z.B. int, long, real). Eine übliche Nutzung einer Gewichtungsspalte besteht darin, die Stichprobenerstellung oder die Bucket-Zuordnung/Aggregation der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.
    
    Beispiel: `T | evaluate autocluster('~', sample_Count)` 

* NumSeeds - *int* [Standard: 25] 

    Die Anzahl von Startwerten bestimmt die Anzahl von anfänglichen lokalen Suchpunkten des Algorithmus. Je nach der Struktur der Daten erhöht sich beim Erhöhen der Anzahl von Startwerten in einigen Fällen die Anzahl (bzw. Qualität) von Ergebnissen, da ein größerer Suchbereich vorhanden ist. Gleichzeitig verlangsamen sich die Abfragen. Der Wert hat abnehmende Ergebnisse in beide Richtungen, so dass die Verringerung unter 5 wird vernachlässigbare Leistungsverbesserungen zu erreichen und Erhöhung über 50 wird selten zusätzliche Muster erzeugen.

    Beispiel: `T | evaluate autocluster('~', '~', 15)`

* CustomWildcard - *"any_value_per_type"*

    Legt den Platzhalterwert für einen bestimmten Typ in der Ergebnistabelle fest, der angibt, dass das aktuelle Muster keine Einschränkung für diese Spalte besitzt.
    Der Standard ist „null“, da der Standard eine leere Zeichenfolge ist. Wenn der Standardwert ein tragfähiger Wert in den Daten ist, `*`sollte ein anderer Platzhalterwert verwendet werden (z. B. ).

    Beispiel: `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**Beispiel**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|SegmentId|Anzahl|Percent|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7||Hagel|Nein
|1|512|8,7||Sturm|YES
|2|898|15,3|TEXAS||

**Beispiel mit benutzerdefinierten Platzhaltern**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|SegmentId|Anzahl|Percent|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7|\*|Hagel|Nein
|1|512|8,7|\*|Sturm|YES
|2|898|15,3|TEXAS|\*|\*

**Siehe auch**

* [basket](./basketplugin.md)
* [Reduzieren](./reduceoperator.md)

**Zusätzliche Informationen**

* AutoCluster basiert zum größten Teil auf dem Seed-Expand-Algorithmus des folgenden Dokuments: [Algorithmen für Data Mining von Telemetriedaten mit diskreten Attributen](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1). 

