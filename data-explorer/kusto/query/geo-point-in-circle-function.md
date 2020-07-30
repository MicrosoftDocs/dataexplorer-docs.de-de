---
title: geo_point_in_circle ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_point_in_circle () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 1e94e8eca72e6cb679a84e7b91ea376b9ec4b29c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347815"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

Berechnet, ob die georäumlichen Koordinaten sich innerhalb eines Kreises auf der Erde befinden.

## <a name="syntax"></a>Syntax

`geo_point_in_circle(`*p_longitude* `, ` *p_latitude* `, ` *pc_longitude* `, ` *pc_latitude* `, ` *c_radius*`)`

## <a name="arguments"></a>Argumente

* *p_longitude*: geografischer Koordinaten Längengrad Wert in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-180, + 180].
* *p_latitude*: geografischer Koordinaten Breitenwert in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-90, + 90].
* *pc_longitude*: der Längengrad Wert für die georäumliche Koordinate der Kreis Mitte in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-180, + 180].
* *pc_latitude*: der Breitengrad Wert für die Kreis Center-geografischer Koordinate in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-90, + 90].
* *c_radius*: Kreis Radius in Meter. Der gültige Wert muss positiv sein.

## <a name="returns"></a>Rückgabe

Gibt an, ob die räumlichen Koordinaten innerhalb eines Kreises liegen. Wenn die Koordinaten oder der Kreis ungültig sind, führt die Abfrage zu einem NULL-Ergebnis.

> [!NOTE]
>* Die georäumlichen Koordinaten werden entsprechend der Darstellung durch das [WGS-84-](https://earth-info.nga.mil/GandG/update/index.php?action=home) Koordinaten Referenzsystem interpretiert.
>* Das [geodätische Datum](https://en.wikipedia.org/wiki/Geodetic_datum) , das zum Messen der Entfernung auf der Erde verwendet wird, ist eine Kugel.
>* Ein Kreis ist eine kugelförmige Obergrenze in der Erde. Der Radius der Obergrenze wird entlang der Oberfläche der Kugel gemessen.

## <a name="examples"></a>Beispiele

Die folgende Abfrage sucht alle Orte in dem Bereich, der durch den folgenden Kreis definiert wird: Radius von 18 km, zentriert um [-122,317404, 47,609119] Koordinaten.

:::image type="content" source="images/geo-point-in-circle-function/circle-seattle.png" alt-text="Orte in naher Seattle":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(longitude:real, latitude:real, place:string)
[
    real(-122.317404), 47.609119, 'Seattle',                   // In circle 
    real(-123.497688), 47.458098, 'Olympic National Forest',   // In exterior of circle  
    real(-122.201741), 47.677084, 'Kirkland',                  // In circle
    real(-122.443663), 47.247092, 'Tacoma',                    // In exterior of circle
    real(-122.121975), 47.671345, 'Redmond',                   // In circle
]
| where geo_point_in_circle(longitude, latitude, -122.317404, 47.609119, 18000)
| project place
```

|place|
|---|
|Seattle|
|Kirkland|
|Redmond|

Storm-Ereignisse in Orlando. Die Ereignisse werden nach 100 km-Koordinaten innerhalb von Orlando-Koordinaten gefiltert und nach Ereignistyp und Hash aggregiert.

:::image type="content" source="images/geo-point-in-circle-function/orlando-storm-events.png" alt-text="Storm-Ereignisse in Orlando":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Im folgenden Beispiel werden NY Taxi-Pickups innerhalb von 10 Metern eines bestimmten Standorts angezeigt. Relevante Pickups werden nach Hash aggregiert.

:::image type="content" source="images/geo-point-in-circle-function/circle-junction.png" alt-text="Pickups in der Nähe von NY Taxi":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

Im folgenden Beispiel wird true zurückgegeben.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

Im folgenden Beispiel wird false zurückgegeben.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

Im folgenden Beispiel wird aufgrund der ungültigen Koordinaten Eingabe ein NULL-Ergebnis zurückgegeben.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

Im folgenden Beispiel wird ein NULL-Ergebnis zurückgegeben, weil die Kreis-Radius-Eingabe ungültig ist.

```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||
