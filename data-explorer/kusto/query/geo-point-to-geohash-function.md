---
title: geo_point_to_geohash ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_point_to_geohash () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c37789ac490814288c7331f0b1ae86b8b2178d67
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226923"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

Berechnet den GeoHash-Zeichen folgen Wert für einen geografischen Standort.

Erfahren Sie mehr über [GeoHash](https://en.wikipedia.org/wiki/Geohash).  

**Syntax**

`geo_point_to_geohash(`*Längengrad* `, ` *Breitengrad* `, ` [*Genauigkeit*]`)`

**Argumente**

* *Längengrad*: Längengrad eines geografischen Standorts. Der Längengrad x wird als gültig betrachtet, wenn x eine reelle Zahl ist, und liegt im Bereich [-180, + 180]. 
* *breiten*Grad: Breitengrad eines geografischen Standorts. Der Breitengrad y wird als gültig betrachtet, wenn y eine reelle Zahl ist, und y liegt im Bereich [-90, + 90]. 
* *Genauigkeit*: ein optionaler `int` , der die angeforderte Genauigkeit definiert. Unterstützte Werte liegen im Bereich [1, 18]. Wenn nicht angegeben, wird der Standardwert `5` verwendet.

**Rückgabe**

Der GeoHash-Zeichen folgen Wert eines angegebenen geografischen Standorts mit angeforderter Genauigkeits Länge. Wenn die Koordinate oder die Genauigkeit ungültig ist, führt die Abfrage zu einem leeren Ergebnis.

> [!NOTE]
>
> * GeoHash kann ein nützliches Georäumliches Clustering-Tool sein.
> * GeoHash hat 18 Genauigkeits Stufen mit Bereichs Abdeckung von 25 Millionen km² auf der höchsten Ebene 1 bis 0,6 ° ² auf der niedrigsten Ebene 18.
> * Allgemeine Präfixe von GeoHash weisen auf die Nähe der einzelnen Punkte hin. Je länger ein frei gegebenes Präfix ist, desto näher sind die beiden Orte. Der Genauigkeits Wert übersetzt in die GeoHash-Länge.
> * GeoHash ist ein rechteckiger Bereich auf einer Ebenen-Oberfläche.
> * Das Aufrufen der [geo_geohash_to_central_point ()](geo-geohash-to-central-point-function.md) -Funktion für eine GeoHash-Zeichenfolge, die für Längengrad x und Breite y berechnet wurde, gibt nicht notwendigerweise x und y zurück.
> * Aufgrund der GeoHash-Definition ist es möglich, dass sich zwei geografische Standorte sehr nah beieinander befinden, aber unterschiedliche GeoHash Codes haben.

**GeoHash rechteckige Flächenabdeckung pro Genauigkeits Wert:**

| Genauigkeit | Breite     | Höhe    |
|----------|-----------|-----------|
| 1        | 5000 km   | 5000 km   |
| 2        | 1250 km   | 625 km    |
| 3        | 156,25 km | 156,25 km |
| 4        | 39,06 km  | 19,53 km  |
| 5        | 4,88 km   | 4,88 km   |
| 6        | 1,22 km   | 0,61 km   |
| 7        | 152,59 Mio.  | 152,59 Mio.  |
| 8        | 38,15 Mio.   | 19,07 Mio.   |
| 9        | 4,77 Mio.    | 4,77 Mio.    |
| 10       | 1,19 Mio.    | 0,59 Mio.    |
| 11       | 149,01 mm | 149,01 mm |
| 12       | 37,25 mm  | 18,63 mm  |
| 13       | 4,66 mm   | 4,66 mm   |
| 14       | 1,16 mm   | 0,58 mm   |
| 15       | 145,52 °  | 145,52 °  |
| 16       | 36,28 °   | 18,19 °   |
| 17       | 4,55 °    | 4,55 °    |
| 18       | 1,14 °    | 0,57 °    |

Siehe auch [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md).

**Beispiele**

Von GeoHash aggregierte US Storm-Ereignisse.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="GeoHash USA":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| GeoHash      |
|--------------|
| xn76m27ty9g4 |

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|GeoHash|
|---|
|dhwfz15h|

Im folgenden Beispiel werden Gruppen von Koordinaten gefunden. Jedes Koordinaten Paar in der Gruppe befindet sich in einem rechteckigen Bereich von 4,88 km um 4,88 km.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

| GeoHash | count | locations  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

Im folgenden Beispiel wird aufgrund der ungültigen Koordinaten Eingabe ein leeres Ergebnis erzeugt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| GeoHash |
|---------|
|         |

Im folgenden Beispiel wird aufgrund der ungültigen Genauigkeits Eingabe ein leeres Ergebnis erzeugt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| GeoHash |
|---------|
|         |
