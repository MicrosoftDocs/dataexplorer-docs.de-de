---
title: geo_point_to_s2cell ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_point_to_s2cell () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: fe3af4218fcc8b714cd4d62e45e78d6f8c9c0270
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226906"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

Berechnet den S2-zelltokenzeichenfolgen-Wert für einen geografischen Standort.

Weitere Informationen finden Sie in der [Zellen Hierarchie S2](https://s2geometry.io/devguide/s2cell_hierarchy).

**Syntax**

`geo_point_to_s2cell(`*Längengrad* `, ` *Breitengrad* `, ` *Ebene*`)`

**Argumente**

* *Längengrad*: Längengrad eines geografischen Standorts. Der Längengrad " *x* " wird als gültig betrachtet, wenn " *x* " eine reelle Zahl und " *x* " im Bereich [-180, + 180] ist. 
* *breiten*Grad: Breitengrad eines geografischen Standorts. Der Breitengrad y wird als gültig betrachtet, wenn y eine reelle Zahl und y im Bereich [-90, + 90] ist. 
* *Level*: ein optionales `int` , das die angeforderte Zellen Ebene definiert. Unterstützte Werte liegen im Bereich [0,0]. Wenn nicht angegeben, wird der Standardwert `11` verwendet.

**Rückgabe**

Der Zeichen folgen Wert des S2-zelltokens eines angegebenen geografischen Standorts. Wenn die Koordinaten oder Ebenen ungültig sind, führt die Abfrage zu einem leeren Ergebnis.

> [!NOTE]
>
> * Die Zelle S2 kann ein nützliches Georäumliches Clustering-Tool sein.
> * Die Zelle S2 umfasst 31 Ebenen der Hierarchie mit Bereichs Abdeckung von 85011, 012.19 km ² auf der höchsten Ebene 0 bis 00.44 cm ² auf niedrigster Ebene 30.
> * In der Zelle S2 wird der Mittelpunkt der Zelle während der Erhöhung der Ebene zwischen 0 und 30 beibehalten.
> * Die S2-Zelle ist eine Zelle auf einer kugelförmigen Oberfläche und deren Ränder sind geodäcs.
> * Das Aufrufen der [geo_s2cell_to_central_point ()](geo-s2cell-to-central-point-function.md) -Funktion für eine S2-zelltokenzeichenfolge, die für Längengrad x und Breite y berechnet wurde, gibt nicht notwendigerweise x und y zurück.
> * Es ist möglich, dass sich zwei geografische Standorte sehr nah beieinander befinden, aber über unterschiedliche S2-zelltoken verfügen.

**S2-Zelle ungefähre Bereichs Abdeckung pro Wert**

Für jede Ebene ist die Größe der S2-Zelle ähnlich, aber nicht genau gleich. Die Zellen Größen in der Nähe sind tendenziell eher gleich.

|Ebene|Minimale Länge der Zufalls Zellen Kante (UK)|Maximale Länge von Zufalls Zellen Kanten (USA)|
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
|12|1699 Mio.|2 km|
|13|850 Mio.|1225 Mio.|
|14|425 Mio.|613 Mio.|
|15|212 Mio.|306 Mio.|
|16|106 Mio.|153 Mio.|
|17|53 Mio.|77 Mio.|
|18|27 Mio.|38 Mio.|
|19|13 min.|19 Mio.|
|20|7 min.|10 Mio.|
|21|3 Mio.|5 Mio.|
|22|166 cm|2 Mio.|
|23|83 cm|120 cm|
|24|41 cm|60 cm|
|25|21 cm|30 cm|
|26|10 cm|15 cm|
|27|5 cm|7 cm|
|28|2 cm|4 cm|
|29|12 mm|18 mm|
|30|6 mm|9 mm|

Die Tabellen Quelle finden Sie [in dieser Ressourcen Statistik Ressource mit S2](https://s2geometry.io/resources/s2cell_statistics).

Siehe auch [geo_point_to_geohash ()](geo-point-to-geohash-function.md).

**Beispiele**

US Storm-Ereignisse, die von s2cell aggregiert werden.

:::image type="content" source="images/geo-point-to-s2cell-function/s2cell.png" alt-text="US-s2cell":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

Im folgenden Beispiel werden Gruppen von Koordinaten gefunden. Jedes Koordinaten Paar in der Gruppe befindet sich in der S2-Zelle mit einem maximalen Bereich von 1632,45 KB.

<!-- csl: https://help.kusto.windows.net/Samples -->
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
| 47b1d  | 2     | ["A", "B"] |
| 47ae3  | 1     | ["C"]     |

Im folgenden Beispiel wird aufgrund der ungültigen Koordinaten Eingabe ein leeres Ergebnis erzeugt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

Im folgenden Beispiel wird aufgrund der ungültigen Stufen Eingabe ein leeres Ergebnis erzeugt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

Im folgenden Beispiel wird aufgrund der ungültigen Stufen Eingabe ein leeres Ergebnis erzeugt.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |
