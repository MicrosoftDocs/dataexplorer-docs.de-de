---
title: geo_point_to_s2cell() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird geo_point_to_s2cell() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 253b850da519aceb0ead9456f7e49d6dd37d78ec
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663740"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

Berechnet den S2-Zellentokenzeichenfolgenwert für einen geografischen Standort.

Weitere Informationen zu S2-Zellen finden Sie [hier](http://s2geometry.io/devguide/s2cell_hierarchy).

**Syntax**

`geo_point_to_s2cell(`*longitude*`, `*Längengrad-Breitengrad*`, `*level*`)`

**Argumente**

* *Längengrad*: Längengradwert einer geografischen Position. Längengrad x gilt als gültig, wenn x eine reelle Zahl ist und x im Bereich [-180, +180] liegt. 
* *Breitengrad*: Breitengradwert eines geografischen Standorts. Breitengrad y gilt als gültig, wenn y eine reelle Zahl und y im Bereich [-90, +90] ist. 
* *level*: `int` Ein optionaler Wert, der die angeforderte Zellenebene definiert. Unterstützte Werte liegen im Bereich [0,30]. Wenn nicht angegeben, `11` wird der Standardwert verwendet.

**Rückgabe**

Der Zeichenfolgenwert des S2-Zellentokens einer bestimmten geografischen Position. Wenn die Koordinate oder Ebene ungültig ist, führt die Abfrage zu einem leeren Ergebnis.

> [!NOTE]
>
> * S2Cell kann ein nützliches räumliches Clustering-Tool sein.
> * S2Cell hat 31 Hierarchieebenen mit Flächenabdeckung von 85.011.012,19 km2 auf der höchsten Ebene 0 bis 00,44 cm2 auf der niedrigsten Ebene 30.
> * S2Cell bewahrt das Zellenzentrum während der Pegelerhöhung von 0 auf 30 gut.
> * S2Cell ist eine Zelle auf einer Kugeloberfläche und ihre Kanten sind geodäsie.
> * Das Aufrufen der [geo_s2cell_to_central_point()-Funktion](geo-s2cell-to-central-point-function.md) für eine s2cell-Tokenzeichenfolge, die für Längengrad x und Breitengrad y berechnet wurde, gibt nicht notwendigerweise x und y zurück.
> * Es ist möglich, dass zwei geografische Standorte sehr nahe beieinander liegen, aber unterschiedliche S2-Zellentoken haben.

**S2 Zell ungefähre Flächenabdeckung pro Füllstandwert**

Für jede Ebene ist die Größe der s2cell ähnlich, aber nicht genau gleich. In der Nähe ZellenGröße in der Regel mehr gleich sein.

|Ebene|Minimale zufällige Zellkantenlänge (UK)|Maximale zufällige Zellkantenlänge (US)|
|--|--|--|
|0|7842 km|7842 km|
|1|3921 km|5004 km|
|2|1825 km|2489 km|
|3|840 km|1310 km|
|4|432 km|636 km|
|5|210 km|315 km|
|6|108 km|156 km|
|7|54 km|78 km|
|8|27 km|39 km|
|9|14 km|20 km|
|10|7 km|10 km|
|11|3 km|5 km|
|12|1699 m|2 km|
|13|850 m|1225 m|
|14|425 m|613 m|
|15|212 m|306 m|
|16|106 m|153 m|
|17|53 m|77 m|
|18|27 m|38 m|
|19|13 m|19 m|
|20|7 m|10 m|
|21|3 m|5 m|
|22|166 cm|2 m|
|23|83 cm|120 cm|
|24|41 cm|60 cm|
|25|21 cm|30 cm|
|26|10 cm|15 cm|
|27|5 cm|7 cm|
|28|2 cm|4 cm|
|29|12 mm|18 mm|
|30|6 mm|9 mm|

Die Tabellenquelle finden Sie [hier](http://s2geometry.io/resources/s2cell_statistics).

Siehe auch [geo_point_to_geohash()](geo-point-to-geohash-function.md).

**Beispiele**

US-Sturmereignisse aggregiert nach s2cell.

:::image type="content" source="images/queries/geo/s2cell.png" alt-text="US s2cell":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

Im folgenden Beispiel werden Gruppen von Koordinaten gefunden. Jedes Koordinatenpaar in der Gruppe befindet sich in s2cell mit einer maximalen Fläche von 1632,45 km2.
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", 10.1234, 53,
  "B", 10.3579, 53,
  "C", 10.6842, 53,
]
| summarize count = count(),                                        // items per group count
            locations = make_list(location_id)                      // items in the group
            by s2cell = geo_point_to_s2cell(longitude, latitude, 8) // s2 cell of the group
```

| s2cell | count | locations |
|--------|-------|-----------|
| 47b1d  | 2     | ["A","B"] |
| 47ae3  | 1     | ["C"]     |

Im folgenden Beispiel wird aufgrund der ungültigen Koordinateneingabe ein leeres Ergebnis erstellt.
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

Im folgenden Beispiel wird aufgrund der ungültigen Ebeneneingabe ein leeres Ergebnis erstellt.
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

Im folgenden Beispiel wird aufgrund der ungültigen Ebeneneingabe ein leeres Ergebnis erstellt.
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |