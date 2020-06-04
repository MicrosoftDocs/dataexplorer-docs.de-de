---
title: 'Basket-Plug-in: Azure Daten-Explorer'
description: Dieser Artikel beschreibt das Warenkorb-Plug-in in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: a43275aa6d2938631cad052cfbdd9a185db487b2
ms.sourcegitcommit: 8953d09101f4358355df60ab09e55e71bc255ead
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420848"
---
# <a name="basket-plugin"></a>Basket-Plugin

```kusto
T | evaluate basket()
```

Mit „Basket“ werden alle häufigen Muster diskreter Attribute (Dimensionen) in den Daten ermittelt und alle häufigen Muster zurückgegeben, die den Häufigkeitsschwellenwert in der ursprünglichen Abfrage überschritten haben. Der Korb findet sicher, dass alle häufigen Muster in den Daten gefunden werden, aber es ist nicht garantiert, dass eine polynomale Laufzeit vorhanden ist, die Laufzeit der Abfrage ist in der Anzahl von Zeilen linear, aber in manchen Fällen ist es möglicherweise exponentiell in der Anzahl der Spalten (Dimensionen). Basket basiert auf dem Apriori-Algorithmus, der ursprünglich für das Data Mining per Basket-Analyse entwickelt wurde.

**Syntax**

`T | evaluate basket(`*Argumente*`)`

**Rückgabe**

Basket gibt alle häufigen Muster zurück, die oberhalb des Schwellenwerts für das Verhältnis (Standard: 0,05) der Zeilen angezeigt werden. Jede Zeile in den Ergebnissen steht für ein Muster.

Die erste Spalte ist die Segment-ID. Die nächsten beiden Spalten sind die Anzahl und der Prozentsatz der Zeilen aus der ursprünglichen Abfrage, die vom Muster aufgezeichnet werden. Die restlichen Spalten stammen aus der ursprünglichen Abfrage. Sie enthalten als Wert entweder einen bestimmten Wert aus der Spalte oder einen Platzhalterwert (standardmäßig NULL) – also variable Werte.

**Argumente (alle optional)**

`T | evaluate basket(`[*Threshold*, *weightcolumn*, *maxdimensions*, *customwildcard*, *customwildcard*,...]`)`

Alle Argumente sind optional, aber sie müssen wie oben angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden sollte, können Sie eine Tilde („~“) angeben (siehe Beispiele unten).

Verfügbare Argumente:

* Schwellenwert-0,015 < *Double* < 1 [Standardwert: 0,05]

    Legt das minimale Verhältnis der Zeilen fest, das als häufig angesehen werden soll (Muster mit einem geringeren Verhältnis werden nicht zurückgegeben).
    
    Beispiel: `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    Berücksichtigt jede Zeile in der Eingabe gemäß dem angegebenen Gewicht (standardmäßig verfügt jede Spalte über eine Gewichtung von „1“). Das Argument muss ein Name einer numerischen Spalte sein (z.B. int, long, real). Eine übliche Nutzung einer Gewichtungsspalte besteht darin, die Stichprobenerstellung oder die Bucket-Zuordnung/Aggregation der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.
    
    Beispiel: `T | evaluate basket('~', sample_Count)`

* Maxdimensions-1 < *int* [Standardwert: 5]

    Legt die maximale Anzahl von nicht korrelierten Dimensionen pro Basket fest – standardmäßig begrenzt, um die Abfragelaufzeit zu verringern.

    Beispiel: `T | evaluate basket('~', '~', 3)`

* Customwildcard- *"any_value_per_type"*

    Legt den Platzhalterwert für einen bestimmten Typ in der Ergebnistabelle fest, der angibt, dass das aktuelle Muster keine Einschränkung für diese Spalte besitzt.
    Der Standard ist „null“, da der Standard eine leere Zeichenfolge ist. Wenn der Standardwert ein in den Daten funktionierender Wert ist, sollte ein anderer Platzhalter Wert verwendet werden (z. b. `*` ).
    Unten finden Sie ein Beispiel hierzu.

    Beispiel: `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|SegmentId|Anzahl|Percent|Staat|EventType|Damage|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4.574|77,7|||Nein|0
|1|2278|38,7||Hagel|Nein|0
|2|5.675|96,4||||0
|3|2371|40,3||Hagel||0
|4|1.279|21,7||Sturm||0
|5|2.468|41,9||Hagel||
|6|1.310|22,3|||YES|
|7|1.291|21,9||Sturm||

**Beispiel mit benutzerdefinierten Platzhaltern**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2, '~', '~', '*', int(-1))
```

|SegmentId|Anzahl|Percent|Staat|EventType|Damage|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4.574|77,7|\*|\*|Nein|0
|1|2278|38,7|\*|Hagel|Nein|0
|2|5.675|96,4|\*|\*|\*|0
|3|2371|40,3|\*|Hagel|\*|0
|4|1.279|21,7|\*|Sturm|\*|0
|5|2.468|41,9|\*|Hagel|\*|-1
|6|1.310|22,3|\*|\*|YES|-1
|7|1.291|21,9|\*|Sturm|\*|-1
