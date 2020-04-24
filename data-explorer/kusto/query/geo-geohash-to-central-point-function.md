---
title: geo_geohash_to_central_point() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird geo_geohash_to_central_point() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a71265b58cffcec2fcf9d6ac8d412c8dd44866f4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514636"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Berechnet die räumlichen Koordinaten, die den Mittelpunkt eines rechteckigen Geohash-Bereichs darstellen.

Weitere Informationen zu Geohash finden Sie unter [Wikipedia](https://en.wikipedia.org/wiki/Geohash).  

**Syntax**

`geo_geohash_to_central_point(`*geohash*`)`

**Argumente**

*geohash*: Geohash-Zeichenfolgenwert, wie er von [geo_point_to_geohash()](geo-point-to-geohash-function.md)berechnet wurde. Die Geohash-Zeichenfolge kann 1 bis 18 Zeichen lang sein.

**Rückgabe**

Die räumlichen Koordinatenwerte im [GeoJSON-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps. Wenn der Geohash ungültig ist, erzeugt die Abfrage ein Nullergebnis.

> [!NOTE]
> Das GeoJSON-Format gibt Längengrad zuerst und Breitengrad Sekunde an.

**Beispiele**

```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|Koordinaten|longitude|latitude|
|---|---|---|---|
|{<br>  "typ": "Punkt",<br>  "Koordinaten": [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

Im folgenden Beispiel wird ein Nullergebnis aufgrund der ungültigen Geohash-Eingabe zurückgegeben.

```kusto
print geohash = geo_geohash_to_central_point("a")
```

|geohash|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>Beispiel: Erstellen von Standort-Deep-Links für Bing Maps

Sie können den Geohash-Wert verwenden, um eine Deeplink-URL zu Bing Maps zu erstellen, indem Sie auf den Geohash-Mittelpunkt zeigen:

```kusto
// Use string concatenation to create Bing Map deep-link URL from a geo-point
let point_to_map_url = (_point:dynamic, _title:string) 
{
    strcat('https://www.bing.com/maps?sp=point.', _point.coordinates[1] ,'_', _point.coordinates[0], '_', url_encode(_title)) 
};
// Convert geohash to center point, and then use 'point_to_map_url' to create Bing Map deep-link
let geohash_to_map_url = (_geohash:string, _title:string)
{
    point_to_map_url(geo_geohash_to_central_point(_geohash), _title)
};
print geohash = 'sv8wzvy7'
| extend url = geohash_to_map_url(geohash, "You are here")
```

|geohash|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|