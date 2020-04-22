---
title: geo_point_in_circle() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird geo_point_in_circle() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: ca3ca8a1ac2299c43888ac827d46c25d02e4999d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663796"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

Berechnet, ob sich die räumlichen Koordinaten innerhalb eines Kreises auf der Erde befinden.

**Syntax**

`geo_point_in_circle(`*p_longitude*`, `*p_latitude*`, `*pc_longitude pc_latitude*`, `*pc_latitude*`, `*c_radius*`)`

**Argumente**

* *p_longitude*: Georäumlicher Koordinatenlängenwert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-180, +180].
* *p_latitude*: Georäumliche Koordinatenbreitenwert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-90, +90].
* *pc_longitude*: Kreiszentrum Georäumliche Koordinaten LängengradWert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-180, +180].
* *pc_latitude*: Kreiszentrum Georäumliche Koordinaten breiten Wert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-90, +90].
* *c_radius*: Kreisradius in Metern. Der gültige Wert muss positiv sein.

**Rückgabe**

Gibt an, ob sich die räumlichen Koordinaten innerhalb eines Kreises befinden. Wenn die Koordinaten oder der Kreis ungültig sind, erzeugt die Abfrage ein Nullergebnis.

> [!NOTE]
>* Die räumlichen Koordinaten werden als durch das [Koordinatenreferenzsystem WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) dargestellt interpretiert.
>* Das [geodätische Datum,](https://en.wikipedia.org/wiki/Geodetic_datum) das verwendet wird, um die Entfernung auf der Erde zu messen, ist eine Kugel.
>* Circle ist eine kugelförmige Kappe auf der Erde. Der Radius der Kappe wird entlang der Oberfläche der Kugel gemessen.

**Beispiele**

Die folgende Abfrage findet alle Orte in dem durch einen Kreis definierten Bereich mit einem Radius von 18 km, dessen Mittelpunkt [-122.317404, 47.609119] Koordinaten ist.

:::image type="content" source="images/queries/geo/circle_seattle.png" alt-text="Orten nah von Seattle":::

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

Sturmereignisse in Orlando. Die Ereignisse werden nach Orlando-Koordinaten innerhalb von 100 km gefiltert und nach Ereignistyp und Hash aggregiert.

:::image type="content" source="images/queries/geo/orlando_storm_events.png" alt-text="Sturminsturm in Orlando":::

```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Das folgende Beispiel zeigt NY Taxi Pickups in der Nähe von einem Ort und innerhalb von 10 Metern. Relevante Pickups werden durch Hash aggregiert.

:::image type="content" source="images/queries/geo/circle_junction.png" alt-text="NY Taxi in der Nähe Pickups":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

Im folgenden Beispiel wird true zurückgegeben.
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

Im folgenden Beispiel wird false zurückgegeben.
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

Im folgenden Beispiel wird ein Nullergebnis aufgrund der ungültigen Koordinateneingabe zurückgegeben.
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

Im folgenden Beispiel wird ein Nullergebnis aufgrund der ungültigen Kreisradiuseingabe zurückgegeben.
```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||