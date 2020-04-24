---
title: geo_s2cell_to_central_point() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird geo_s2cell_to_central_point() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 624d163b508768d0a5316ee3ec62a12b11942176
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514432"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

Berechnet die räumlichen Koordinaten, die den Mittelpunkt der S2-Zelle darstellen.

Weitere Informationen zu S2-Zellen finden Sie [hier](http://s2geometry.io/devguide/s2cell_hierarchy).

**Syntax**

`geo_s2cell_to_central_point(`*s2cell*`)`

**Argumente**

*s2cell*: S2 Cell Token string value as it was calculated by [geo_point_to_s2cell()](geo-point-to-s2cell-function.md). Die maximale Zeichenfolgenlänge des S2-Zellentokens beträgt 16 Zeichen.

**Rückgabe**

Die räumlichen Koordinatenwerte im [GeoJSON-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps. Wenn das S2-Zellentoken ungültig ist, erzeugt die Abfrage ein Nullergebnis.

> [!NOTE]
> Das GeoJSON-Format gibt Längengrad zuerst und Breitengrad Sekunde an.

**Beispiele**

```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|Koordinaten|longitude|latitude|
|---|---|---|---|
|{<br>  "typ": "Punkt",<br>  "Koordinaten": [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

Im folgenden Beispiel wird ein Nullergebnis aufgrund der ungültigen S2-Zellentokeneingabe zurückgegeben.
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||