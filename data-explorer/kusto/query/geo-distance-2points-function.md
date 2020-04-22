---
title: geo_distance_2points() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird geo_distance_2points() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 30a26bbcc57ecde2ac3beeeb1483b953a0cc5820
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030151"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

Berechnet den kürzesten Abstand zwischen zwei Geokoordinaten auf der Erde.

**Syntax**

`geo_distance_2points(`*p1_longitude*`, `*p1_latitude p2_longitude*`, `*p2_longitude*`, `*p2_latitude*`)`

**Argumente**

* *p1_longitude*: Erste Geo-Koordinate, Längengradwert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-180, +180].
* *p1_latitude*: Erste Geo-Koordinate, Breitengrad wert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-90, +90].
* *p2_longitude*: Zweite Geo-Koordinate, Längengradwert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-180, +180].
* *p2_latitude*: Zweite Geo-Koordinate, Breitengrad wert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-90, +90].

**Rückgabe**

Die kürzeste Entfernung, in Metern, zwischen zwei geographischen Standorten auf der Erde. Wenn die Koordinaten ungültig sind, erzeugt die Abfrage ein Nullergebnis.

> [!NOTE]
> * Die räumlichen Koordinaten werden als durch das [Koordinatenreferenzsystem WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) dargestellt interpretiert.
> * Das [geodätische Datum,](https://en.wikipedia.org/wiki/Geodetic_datum) das verwendet wird, um die Entfernung auf der Erde zu messen, ist eine Kugel.

**Beispiele**

Im folgenden Beispiel wird die kürzeste Entfernung zwischen Seattle und Los Angeles gefunden.


:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="Distanz zwischen Seattle und Los Angeles":::

```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

Hier ist eine Annäherung des kürzesten Weges von Seattle nach London. Die Linie besteht aus Koordinaten entlang der LineString und innerhalb von 500 Metern von ihr entfernt.

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="Seattle nach London LineString":::

```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Im folgenden Beispiel werden alle Zeilen gefunden, in denen der kürzeste Abstand zwischen zwei Koordinaten zwischen 1 und 11 Metern liegt.
```kusto
StormEvents
| extend distance_1_to_11m = geo_distance_2points(BeginLon, BeginLat, EndLon, EndLat)
| where distance_1_to_11m between (1 .. 11)
| project distance_1_to_11m
```

| distance_1_to_11m |
|-------------------|
| 10.5723100154958  |
| 7.92153588248414  |

Im folgenden Beispiel wird ein Nullergebnis aufgrund der ungültigen Koordinateneingabe zurückgegeben.
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |