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
ms.openlocfilehash: cf83690d61bb84d1b6b877e76a77d5776be35ad4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349243"
---
# <a name="basket-plugin"></a>basket-Plug-In

```kusto
T | evaluate basket()
```

Basket findet alle häufigen Muster diskreter Attribute (Dimensionen) in den Daten. Anschließend werden die häufigen Muster zurückgegeben, die den Häufigkeits Schwellenwert in der ursprünglichen Abfrage überschritten haben. Der Warenkorb findet sicher jedes häufige Muster in den Daten, aber es ist nicht garantiert, dass eine polynomale Laufzeit vorhanden ist. Die Laufzeit der Abfrage ist in der Anzahl der Zeilen linear, kann aber exponentiell in der Anzahl von Spalten (Dimensionen) sein. Basket basiert auf dem Apriori-Algorithmus, der ursprünglich für das Data Mining per Basket-Analyse entwickelt wurde.

## <a name="syntax"></a>Syntax

`T | evaluate basket(`*Argumente*`)`

## <a name="returns"></a>Rückgabe

Basket gibt alle häufigen Muster zurück, die oberhalb des Verhältnis Schwellenwerts der Zeilen angezeigt werden. Der Standard Schwellenwert ist 0,05. Jede Zeile in den Ergebnissen steht für ein Muster.

Die erste Spalte ist die Segment-ID. Die nächsten beiden Spalten sind die *Anzahl* und der *Prozentsatz der Zeilen*aus der ursprünglichen Abfrage, die vom Muster aufgezeichnet werden. Die übrigen Spalten stammen aus der ursprünglichen Abfrage.
Der Wert ist entweder ein bestimmter Wert aus der Spalte oder ein Platzhalter Wert, der standardmäßig NULL ist, d. h. ein Variablen Wert.

**Argumente (alle optional)**

`T | evaluate basket(`[*Threshold*, *weightcolumn*, *maxdimensions*, *customwildcard*, *customwildcard*,...]`)`

Alle Argumente sind optional, aber sie müssen wie oben angegeben sortiert werden. Um anzugeben, dass der Standardwert verwendet werden soll, verwenden Sie die Zeichenfolge Tilde value-' ~ '. Siehe folgende Beispiele.

Verfügbare Argumente:

* Schwellenwert-0,015 < *Double* < 1 [Standardwert: 0,05]

    Legt das minimale Verhältnis der Zeilen fest, das als häufig angesehen werden soll. Muster mit einem kleineren Verhältnis werden nicht zurückgegeben.
    
    Beispiel: `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    Berücksichtigt jede Zeile in der Eingabe entsprechend der angegebenen Gewichtung. Standardmäßig hat jede Zeile eine Gewichtung von "1". Das Argument muss ein Name einer numerischen Spalte sein, z. b. "int", "Long", "Real". Eine allgemeine Verwendung einer Gewichtungs Spalte besteht darin, die Stichprobenentnahme oder das Einbetten/Aggregieren der Daten zu berücksichtigen, die bereits in die einzelnen Zeilen eingebettet sind.

    Beispiel: `T | evaluate basket('~', sample_Count)`

* Maxdimensions-1 < *int* [Standardwert: 5]

    Legt die maximale Anzahl nicht korrelierter Dimensionen pro Warenkorb (Standardmäßig eingeschränkt) fest, um die Abfrage Laufzeit zu minimieren.

    Beispiel: `T | evaluate basket('~', '~', 3)`

* Customwildcard- *"any_value_per_type"*

    Legt den Platzhalterwert für einen bestimmten Typ in der Ergebnistabelle fest, der angibt, dass das aktuelle Muster keine Einschränkung für diese Spalte besitzt.
    Der Standardwert lautet null. Der Standardwert für eine Zeichenfolge ist eine leere Zeichenfolge. Wenn der Standardwert ein guter Wert in den Daten ist, sollte ein anderer Platzhalter Wert verwendet werden, z `*` . b..

    Zum Beispiel:

     `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|SegmentId|Anzahl|Percent|Zustand|EventType|Damage|DamageCrops|
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

|SegmentId|Anzahl|Percent|Zustand|EventType|Damage|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4.574|77,7|\*|\*|Nein|0
|1|2278|38,7|\*|Hagel|Nein|0
|2|5.675|96,4|\*|\*|\*|0
|3|2371|40,3|\*|Hagel|\*|0
|4|1.279|21,7|\*|Sturm|\*|0
|5|2.468|41,9|\*|Hagel|\*|-1
|6|1.310|22,3|\*|\*|YES|-1
|7|1.291|21,9|\*|Sturm|\*|-1
