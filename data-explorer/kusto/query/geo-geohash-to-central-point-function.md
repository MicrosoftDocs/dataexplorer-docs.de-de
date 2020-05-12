---
title: geo_geohash_to_central_point ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_geohash_to_central_point () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: eb59eae0bc014c6ce9060d65f6c3aced80e4275c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227127"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Berechnet die räumlichen Koordinaten, die den Mittelpunkt eines GeoHash-rechteckigen Bereichs darstellen.

Weitere Informationen finden Sie hier [`geohash`](https://en.wikipedia.org/wiki/Geohash) .  

**Syntax**

`geo_geohash_to_central_point(`*GeoHash*`)`

**Argumente**

*GeoHash*: GeoHash Zeichen folgen Wert, wie er von [geo_point_to_geohash ()](geo-point-to-geohash-function.md)berechnet wurde. Die GeoHash-Zeichenfolge kann zwischen 1 und 18 Zeichen enthalten.

**Rückgabe**

Die georäumlichen Koordinaten Werte im [geojson-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps. Wenn GeoHash ungültig ist, erzeugt die Abfrage ein NULL-Ergebnis.

> [!NOTE]
> Das geojson-Format gibt die Längen-und Breitengrad Sekunde an.

**Beispiele**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|Koordinaten|longitude|latitude|
|---|---|---|---|
|{<br>  "Type": "Point",<br>  "Koordinaten": [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

Im folgenden Beispiel wird ein NULL-Ergebnis zurückgegeben, da die GeoHash-Eingabe ungültig ist.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_geohash_to_central_point("a")
```

|GeoHash|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>Beispiel: Erstellen von Deep-Links für den Standort

Sie können den GeoHash-Wert verwenden, um eine Deep-Link-URL zu den Tabellen mit dem Typ "GeoHash" zu erstellen:

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|GeoHash|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|
