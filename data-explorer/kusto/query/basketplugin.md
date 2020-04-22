---
title: Korb-Plugin - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Warenkorb-Plugin in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: 06fd1e33624bca6aee18a1ca969af18656c6b3e0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663886"
---
# <a name="basket-plugin"></a>Korb-Plugin

```kusto
T | evaluate basket()
```

Mit „Basket“ werden alle häufigen Muster diskreter Attribute (Dimensionen) in den Daten ermittelt und alle häufigen Muster zurückgegeben, die den Häufigkeitsschwellenwert in der ursprünglichen Abfrage überschritten haben. Korb wird garantiert alle häufigen Muster in den Daten finden, aber es ist nicht garantiert, dass Polynomiallaufzeit hat, die Laufzeit der Abfrage ist linear in der Anzahl der Zeilen, aber in einigen Fällen kann exponentiell in der Anzahl der Spalten (Dimensionen) sein. Basket basiert auf dem Apriori-Algorithmus, der ursprünglich für das Data Mining per Basket-Analyse entwickelt wurde.

**Syntax**

`T | evaluate basket(`*Argumente*`)`

**Rückgabe**

Der Basket gibt alle häufigen Muster zurück, die über dem Verhältnisschwellenwert (Standard: 0,05) der Zeilen liegen. Jede Zeile in den Ergebnissen steht für ein Muster.

Die erste Spalte ist die Segment-ID. Die nächsten beiden Spalten sind die Anzahl und der Prozentsatz der Zeilen aus der ursprünglichen Abfrage, die vom Muster erfasst werden. Die restlichen Spalten stammen aus der ursprünglichen Abfrage. Sie enthalten als Wert entweder einen bestimmten Wert aus der Spalte oder einen Platzhalterwert (standardmäßig NULL) – also variable Werte.

**Argumente (alle optional)**

`T | evaluate basket(`[*Schwellenwert*, *WeightColumn*, *MaxDimensions*, *CustomWildcard*, *CustomWildcard*, ...]`)`

Alle Argumente sind optional, aber sie müssen wie oben angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden sollte, können Sie eine Tilde („~“) angeben (siehe Beispiele unten).

Verfügbare Argumente:

* Schwellenwert - 0,015 < *doppel* < 1 [Standard: 0,05]

    Legt das minimale Verhältnis der Zeilen fest, das als häufig angesehen werden soll (Muster mit einem geringeren Verhältnis werden nicht zurückgegeben).
    
    Beispiel: `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    Berücksichtigt jede Zeile in der Eingabe gemäß dem angegebenen Gewicht (standardmäßig verfügt jede Spalte über eine Gewichtung von „1“). Das Argument muss ein Name einer numerischen Spalte sein (z.B. int, long, real). Eine übliche Nutzung einer Gewichtungsspalte besteht darin, die Stichprobenerstellung oder die Bucket-Zuordnung/Aggregation der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.
    
    Beispiel: `T | evaluate basket('~', sample_Count)`

* MaxDimensions - 1 < *int* [Standard: 5]

    Legt die maximale Anzahl von nicht korrelierten Dimensionen pro Basket fest – standardmäßig begrenzt, um die Abfragelaufzeit zu verringern.

    Beispiel: `T | evaluate basket('~', '~', 3)`

* CustomWildcard - *"any_value_per_type"*

    Legt den Platzhalterwert für einen bestimmten Typ in der Ergebnistabelle fest, der angibt, dass das aktuelle Muster keine Einschränkung für diese Spalte besitzt.
    Der Standard ist „null“, da der Standard eine leere Zeichenfolge ist. Wenn der Standardwert ein tragfähiger Wert in den Daten ist, `*`sollte ein anderer Platzhalterwert verwendet werden (z. B. ).
    Unten finden Sie ein Beispiel hierzu.

    Beispiel: `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**Beispiel**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|SegmentId|Anzahl|Percent|State|EventType|Damage|DamageCrops|
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

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2, '~', '~', '*', int(-1))
```

|SegmentId|Anzahl|Percent|State|EventType|Damage|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4.574|77,7|\*|\*|Nein|0
|1|2278|38,7|\*|Hagel|Nein|0
|2|5.675|96,4|\*|\*|\*|0
|3|2371|40,3|\*|Hagel|\*|0
|4|1.279|21,7|\*|Sturm|\*|0
|5|2.468|41,9|\*|Hagel|\*|-1
|6|1.310|22,3|\*|\*|YES|-1
|7|1.291|21,9|\*|Sturm|\*|-1
