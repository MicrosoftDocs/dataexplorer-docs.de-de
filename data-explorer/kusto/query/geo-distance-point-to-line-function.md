---
title: geo_distance_point_to_line() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden geo_distance_point_to_line() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 34c6811ed5a51dae5281a4374421df5914c5b263
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663782"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

Berechnet den kürzesten Abstand zwischen einer Koordinate und einer Linie auf der Erde.

**Syntax**

`geo_distance_point_to_line(`*longitude*`, `*Längengrad-BreitengradlinieString*`, `*lineString*`)`

**Argumente**

* *Längengrad*: Georäumliche Koordinaten Längengradwert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-180, +180].
* *Breitengrad*: Georäumliche Koordinatenbreitenwert in Grad. Gültiger Wert ist eine reelle Zahl und im Bereich [-90, +90].
* *lineString*: Zeile im [GeoJSON-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps.

**Rückgabe**

Die kürzeste Entfernung, in Metern, zwischen einer Koordinate und einer Linie auf der Erde. Wenn die Koordinate oder lineString ungültig sind, erzeugt die Abfrage ein Nullergebnis.

> [!NOTE]
> * Die räumlichen Koordinaten werden als durch das [Koordinatenreferenzsystem WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) dargestellt interpretiert.
> * Das [geodätische Datum,](https://en.wikipedia.org/wiki/Geodetic_datum) das verwendet wird, um die Entfernung auf der Erde zu messen, ist eine Kugel. Linienkanten sind Geodäsie auf der Kugel.

**LineString-Definition und Einschränkungen**

dynamic('type': "LineString","coordinates": [ [lng_1,lat_1], [lng_2,lat_2] ,..., [lng_N,lat_N] ])

* Das LineString-Koordinatenarray muss mindestens zwei Einträge enthalten.
* Koordinaten [Längengrad, Breitengrad] müssen gültig sein, wenn Längengrad eine reelle Zahl im Bereich [-180, +180] und Breitengrad eine reelle Zahl im Bereich [-90, +90] ist.
* Die Kantenlänge muss weniger als 180 Grad betragen. Die kürzeste Kante zwischen den beiden Scheitelpunkten wird gewählt.

> [!TIP]
> Verwenden Sie für eine bessere Leistung literale Zeilen.

**Beispiele**

Im folgenden Beispiel wird die kürzeste Entfernung zwischen dem Flughafen North Las Vegas und einer nahe gelegenen Straße gefunden.

:::image type="content" source="images/queries/geo/distance_point_to_line.png" alt-text="Entfernung zwischen North Las Vegas Airport und road":::

```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

Sturmereignisse an der Südküste der USA. Die Ereignisse werden nach einer maximalen Entfernung von 5 km von der definierten Uferlinie gefiltert.

:::image type="content" source="images/queries/geo/us_south_coast_storm_events.png" alt-text="Sturmereignisse an der US-Südküste":::

```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

NY Taxi Pickups. Pickups werden nach einem maximalen Abstand von 0,1 m von der definierten Linie gefiltert.

:::image type="content" source="images/queries/geo/park_ave_ny_road.png" alt-text="Sturmereignisse an der US-Südküste":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Im folgenden Beispiel wird ein Nullergebnis aufgrund der ungültigen LineString-Eingabe zurückgegeben.
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

Im folgenden Beispiel wird ein Nullergebnis aufgrund der ungültigen Koordinateneingabe zurückgegeben.
```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |