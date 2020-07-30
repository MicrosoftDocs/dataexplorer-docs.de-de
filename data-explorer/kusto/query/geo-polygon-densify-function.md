---
title: geo_polygon_densify ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_polygon_densify () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: bdb7bda617085ae1a7b3ead60c46c80c883943f4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347781"
---
# <a name="geo_polygon_densify"></a>geo_polygon_densify()

Konvertiert Polygon-oder MultiPolygon-planare Ränder in geodäme durch Hinzufügen von zwischen Punkten.

## <a name="syntax"></a>Syntax

`geo_polygon_densify(`*Polygon* `, ` *Toleranz*`)`

## <a name="arguments"></a>Argumente

* *Polygon*: Polygon oder MultiPolygon im [geojson-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps.
* *Toleranz*: ein optionaler numerischer Wert, der den maximalen Abstand zwischen dem ursprünglichen planaren Edge und der konvertierten geodäschen edgekette in Meter definiert. Unterstützte Werte liegen im Bereich [0,1, 10000]. Wenn nicht angegeben, wird der Standardwert `10` verwendet.

## <a name="returns"></a>Rückgabe

Falsch [formatierte Polygon im geojson-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps. Wenn das Polygon oder die Toleranz ungültig ist, führt die Abfrage zu einem NULL-Ergebnis.

> [!NOTE]
> * Die georäumlichen Koordinaten werden entsprechend der Darstellung durch das [WGS-84-](https://earth-info.nga.mil/GandG/update/index.php?action=home) Koordinaten Referenzsystem interpretiert.
> * Das Polygon muss ordnungsgemäß definiert sein, aber die Funktion überprüft die Polygon Gültigkeit nicht.

**Polygon Definition**

Dynamic ({"Type": "Polygon", "Koordinaten": [linearringshell, LinearRingHole_1,..., LinearRingHole_N]})

Dynamic ({"Type": "MultiPolygon", "Koordinaten": [[linearringshell, LinearRingHole_1,..., LinearRingHole_N],..., [linearringshell, LinearRingHole_1,..., LinearRingHole_M]]})

* Linearringshell ist erforderlich und wird als `counterclockwise` geordnetes Array von Koordinaten [[lng_1, lat_1],..., [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]] definiert. Es darf nur eine Shell vorhanden sein.
* Linearringhole ist optional und wird als `clockwise` geordnetes Array von Koordinaten [[lng_1, lat_1],..., [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]] definiert. Es können beliebig viele innere Ringe und Löcher vorhanden sein.
* LinearRing Scheitel Punkte müssen sich mit mindestens drei Koordinaten unterscheiden. Die erste Koordinate muss gleich dem letzten sein. Es sind mindestens vier Einträge erforderlich.
* Die Koordinaten [Längengrad, Breitengrad] müssen gültig sein. Der Längengrad muss eine reelle Zahl im Bereich [-180, + 180] sein, und der Breitengrad muss eine reelle Zahl im Bereich [-90, + 90] sein.
* Linearringshell umfasst höchstens die Hälfte der Kugel. LinearRing teilt die Kugel in zwei Bereiche auf. Die kleinere der beiden Regionen wird ausgewählt.
* Die Länge des LinearRing-Kanten muss weniger als 180 Grad betragen. Der kürzeste Rand zwischen den beiden Scheitel Punkten wird ausgewählt.

**Einschränkungen**

* Die maximale Anzahl von Punkten im nicht sifizierten Polygon ist auf 10485760 beschränkt.
* Das Speichern von Polygonen im [dynamischen](./scalar-data-types/dynamic.md) Format weist Größenbeschränkungen auf.
* Wenn Sie ein gültiges Polygon bezeichnen, wird es möglicherweise ungültig. Der Algorithmus fügt Punkte auf nicht einheitliche Weise hinzu und kann dazu führen, dass die Ränder miteinander interagieren.

**Motivation**

* Im [geojson-Format](https://tools.ietf.org/html/rfc7946) wird ein Rand zwischen zwei Punkten als gerade kartesische Linie definiert.
* Die Entscheidung, geodätische-oder planare Kanten zu verwenden, hängt möglicherweise vom DataSet ab und ist besonders in langen Kanten relevant.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird das Polygon-Polygon im zentralen Park verweigert. Die Ränder sind kurz, und der Abstand zwischen den planaren Rändern und ihren geodäschen Entsprechungen ist kleiner als der von Tolerance angegebene Abstand. Das Ergebnis bleibt unverändert.

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[-73.958244,40.800719],[-73.949146,40.79695],[-73.973093,40.764226],[-73.982062,40.768159],[-73.958244,40.800719]]]})))
```

|densified_polygon|
|---|
|{"Type": "Polygon", "Koordinaten": [[[-73.958244, 40.800719], [-73.949146, 40.79695], [-73.973093, 40.764226], [-73.982062, 40.768159], [-73.958244, 40.800719]]}|

Im folgenden Beispiel werden zwei Ränder des Polygons densiert. Die Länge der nicht identifizierten Kanten beträgt ~ 110 km

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]})))
```

|densified_polygon|
|---|
|{"Type": "Polygon", "Koordinaten": [[[10, 10], [10.25, 10], [10.5, 10], [10,75, 10], [11, 10], [11, 11], [10,75, 11], [10.5, 11], [10.25, 11], [10, 11], [10, 10]]}|

Im folgenden Beispiel wird aufgrund der ungültigen Koordinaten Eingabe ein NULL-Ergebnis zurückgegeben.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,900],[11,10],[11,11],[10,11],[10,10]]]}))
```

|densified_polygon|
|---|
||

Das folgende Beispiel gibt ein NULL-Ergebnis zurück, weil ungültige Toleranz Eingaben eingegeben wurden.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]}), 0)
```

|densified_polygon|
|---|
||
