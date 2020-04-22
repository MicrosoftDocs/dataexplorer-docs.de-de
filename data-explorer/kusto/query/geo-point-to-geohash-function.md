---
title: geo_point_to_geohash() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird geo_point_to_geohash() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: b69d22c56cef4b54a99aa9aa3e9897a2ef177834
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030111"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

Berechnet den Geohash-Zeichenfolgenwert für eine geografische Position.

Weitere Informationen zu Geohash finden Sie [hier](https://en.wikipedia.org/wiki/Geohash).  

**Syntax**

`geo_point_to_geohash(`*Längengrad*`, ``, `[*Genauigkeit*]*latitude*`)`

**Argumente**

* *Längengrad*: Längengradwert einer geografischen Position. Längengrad x gilt als gültig, wenn x eine reelle Zahl ist und sich im Bereich [-180, +180] befindet. 
* *Breitengrad*: Breitengradwert eines geografischen Standorts. Breitengrad y gilt als gültig, wenn y eine reelle Zahl ist und y im Bereich [-90, +90] liegt. 
* *Genauigkeit*: `int` Ein optionaler, der die angeforderte Genauigkeit definiert. Unterstützte Werte liegen im Bereich [1,18]. Wenn nicht angegeben, `5` wird der Standardwert verwendet.

**Rückgabe**

Der Geohash-Zeichenfolgenwert einer bestimmten geografischen Position mit der angeforderten Genauigkeitslänge. Wenn die Koordinate oder Genauigkeit ungültig ist, führt die Abfrage zu einem leeren Ergebnis.

> [!NOTE]
>
> * Geohash kann ein nützliches räumliches Clustering-Tool sein.
> * Geohash hat 18 Genauigkeitsstufen mit einer Flächenabdeckung von 25 Millionen km2 auf der höchsten Stufe 1 bis 0,6 '2 auf der niedrigsten Ebene 18.
> * Das gemeinsame Präfix von Geohashes gibt auf die Nähe von Punkten zueinander hin. Je länger ein gemeinsames Präfix ist, desto näher sind die beiden Orte. Genauigkeitswert übersetzt in Geohash-Länge.
> * Geohash ist ein rechteckiger Bereich auf einer Ebenenfläche.
> * Wenn Sie die [geo_geohash_to_central_point()-Funktion](geo-geohash-to-central-point-function.md) auf einer Geohash-Zeichenfolge aufrufen, die für Längengrad x und Breitengrad y berechnet wurde, wird nicht unbedingt x und y zurückgegeben.
> * Aufgrund der Geohash-Definition ist es möglich, dass zwei geografische Standorte sehr nahe beieinander liegen, aber unterschiedliche Geohash-Codes haben.

**Geohash rechteckige Flächenabdeckung pro Genauigkeitswert:**

| Genauigkeit | Breite     | Höhe    |
|----------|-----------|-----------|
| 1        | 5000 km   | 5000 km   |
| 2        | 1250 km   | 625 km    |
| 3        | 156,25 km | 156,25 km |
| 4        | 39,06 km  | 19,53 km  |
| 5        | 4,88 km   | 4,88 km   |
| 6        | 1,22 km   | 0,61 km   |
| 7        | 152,59 m  | 152,59 m  |
| 8        | 38,15 m   | 19,07 m   |
| 9        | 4,77 m    | 4,77 m    |
| 10       | 1,19 m    | 0,59 m    |
| 11       | 149,01 mm | 149,01 mm |
| 12       | 37,25 mm  | 18,63 mm  |
| 13       | 4,66 mm   | 4,66 mm   |
| 14       | 1,16 mm   | 0,58 mm   |
| 15       | 145,52 €  | 145,52 €  |
| 16       | 36,28 €   | 18,19 €   |
| 17       | 4,55 €    | 4,55 €    |
| 18       | 1,14 €    | 0,57 €    |

Siehe auch [geo_point_to_s2cell()](geo-point-to-s2cell-function.md).

**Beispiele**

US-Sturmereignisse aggregiert durch Geohash.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="US Geohash":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| geohash      |
|--------------|
| xn76m27ty9g4 |

```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|geohash|
|---|
|dhwfz15h|

Im folgenden Beispiel werden Gruppen von Koordinaten gefunden. Jedes Koordinatenpaar der Gruppe befindet sich in einer rechteckigen Fläche von 4,88 km auf 4,88 km.
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", double(-122.303404), 47.570482,
  "B", double(-122.304745), 47.567052,
  "C", double(-122.278156), 47.566936,
]
| summarize count = count(),                                          // items per group count
            locations = make_list(location_id)                        // items in the group
            by geohash = geo_point_to_geohash(longitude, latitude)    // geohash of the group
```

| geohash | count | locations  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

Im folgenden Beispiel wird aufgrund der ungültigen Koordinateneingabe ein leeres Ergebnis erstellt.
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| geohash |
|---------|
|         |

Im folgenden Beispiel wird aufgrund der ungültigen Genauigkeitseingabe ein leeres Ergebnis erstellt.
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| geohash |
|---------|
|         |