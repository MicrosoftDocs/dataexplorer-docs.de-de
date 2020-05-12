---
title: geo_s2cell_to_central_point ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_s2cell_to_central_point () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 7eabcb3cb0c3fd001290848e73bb534ff8ea4218
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226821"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

Berechnet die räumlichen Koordinaten, die den Mittelpunkt einer S2-Zelle darstellen.

Weitere Informationen finden Sie in der [Zellen Hierarchie S2](https://s2geometry.io/devguide/s2cell_hierarchy).

**Syntax**

`geo_s2cell_to_central_point(`*s2cell*`)`

**Argumente**

*s2cell*: S2-zelltokenzeichenfolgen-Wert, wie er von [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md)berechnet wurde. Die maximale Zeichen folgen Länge für den S2-zelltoken beträgt 16 Zeichen.

**Rückgabe**

Die georäumlichen Koordinaten Werte im [geojson-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps. Wenn das S2-zelltoken ungültig ist, führt die Abfrage zu einem NULL-Ergebnis.

> [!NOTE]
> Das geojson-Format gibt die Längen-und Breitengrad Sekunde an.

**Beispiele**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|Koordinaten|longitude|latitude|
|---|---|---|---|
|{<br>  "Type": "Point",<br>  "Koordinaten": [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

Im folgenden Beispiel wird ein NULL-Ergebnis zurückgegeben, weil die Eingabe des ungültigen zelltokens für S2 ist.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||
