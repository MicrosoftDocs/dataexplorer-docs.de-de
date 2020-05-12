---
title: geo_distance_2points ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_distance_2points () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 64c61bcde7bfe02d55bb0f4a6719e9d7b1c1a681
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227416"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

Berechnet den kürzesten Abstand zwischen zwei geografischen Koordinaten auf der Erde.

**Syntax**

`geo_distance_2points(`*p1_longitude* `, ` *p1_latitude* `, ` *p2_longitude* `, ` *p2_latitude*`)`

**Argumente**

* *p1_longitude*: erste georäumliche Koordinate, Längengrad Wert in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-180, + 180].
* *p1_latitude*: erste georäumliche Koordinate, Breitengrad Wert in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-90, + 90].
* *p2_longitude*: zweite georäumliche Koordinate, Längengrad Wert in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-180, + 180].
* *p2_latitude*: zweite georäumliche Koordinate, Breitengrad Wert in Grad. Der gültige Wert ist eine reelle Zahl und liegt im Bereich [-90, + 90].

**Rückgabe**

Die kürzeste Entfernung in Meter zwischen zwei geografischen Standorten auf der Erde. Wenn die Koordinaten ungültig sind, führt die Abfrage zu einem NULL-Ergebnis.

> [!NOTE]
> * Die georäumlichen Koordinaten werden entsprechend der Darstellung durch das [WGS-84-](https://earth-info.nga.mil/GandG/update/index.php?action=home) Koordinaten Referenzsystem interpretiert.
> * Das [geodätische Datum](https://en.wikipedia.org/wiki/Geodetic_datum) , das zum Messen der Entfernung auf der Erde verwendet wird, ist eine Kugel.

**Beispiele**

Im folgenden Beispiel wird die kürzeste Entfernung zwischen Seattle und Los Angeles ermittelt.

:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="Abstand zwischen Seattle und Los Angeles":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754,35197381   |

Im folgenden finden Sie eine Näherung des kürzesten Pfads von Seattle nach London. Die Linie besteht aus Koordinaten entlang der LineString und innerhalb von 500 Meter.

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="Seattle in London LineString":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Im folgenden Beispiel werden alle Zeilen gesucht, in denen der kürzeste Abstand zwischen zwei Koordinaten zwischen 1 und 11 Meter liegt.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

Im folgenden Beispiel wird aufgrund der ungültigen Koordinaten Eingabe ein NULL-Ergebnis zurückgegeben.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |
