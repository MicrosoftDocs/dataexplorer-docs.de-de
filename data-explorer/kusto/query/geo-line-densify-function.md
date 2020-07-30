---
title: geo_line_densify ()-Azure Daten-Explorer
description: In diesem Artikel wird geo_line_densify () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: c5a66255f719d3bd0da962a8eb9d3cae23a8c254
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347832"
---
# <a name="geo_line_densify"></a>geo_line_densify()

Konvertiert planare Zeilen Kanten durch Hinzufügen von zwischen Punkten in geodäk.

## <a name="syntax"></a>Syntax

`geo_line_densify(`*LineString* `, ` *Toleranz*`)`

## <a name="arguments"></a>Argumente

* *LineString*: Zeile im [geojson-Format](https://tools.ietf.org/html/rfc7946) und eines [dynamischen](./scalar-data-types/dynamic.md) Datentyps.
* *Toleranz*: ein optionaler numerischer Wert, der den maximalen Abstand zwischen dem ursprünglichen planaren Edge und der konvertierten geodäschen edgekette in Meter definiert. Unterstützte Werte liegen im Bereich [0,1, 10000]. Wenn nicht angegeben, wird der Standardwert `10` verwendet.

## <a name="returns"></a>Rückgabe

Eine Zeile im [geojson-Format](https://tools.ietf.org/html/rfc7946) und einen [dynamischen](./scalar-data-types/dynamic.md) Datentyp. Wenn die Zeile oder die Toleranz ungültig ist, führt die Abfrage zu einem NULL-Ergebnis.

> [!NOTE]
> * Die georäumlichen Koordinaten werden entsprechend der Darstellung durch das [WGS-84-](https://earth-info.nga.mil/GandG/update/index.php?action=home) Koordinaten Referenzsystem interpretiert.

**LineString-Definition**

Dynamic ({"Type": "LineString", "Koordinaten": [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

* Das LineString-Koordinaten Array muss mindestens zwei Einträge enthalten.
* Die Koordinaten [Längengrad, Breitengrad] müssen gültig sein. Der Längengrad muss eine reelle Zahl im Bereich [-180, + 180] sein, und der Breitengrad muss eine reelle Zahl im Bereich [-90, + 90] sein.
* Die Kantenlänge muss weniger als 180 Grad betragen. Der kürzeste Rand zwischen den beiden Scheitel Punkten wird ausgewählt.

**Einschränkungen**

* Die maximale Anzahl von Punkten in der nicht überprüften Zeile ist auf 10485760 beschränkt.
* Das Speichern von Zeilen im [dynamischen](./scalar-data-types/dynamic.md) Format weist Größenbeschränkungen auf.

**Motivation**

* Im [geojson-Format](https://tools.ietf.org/html/rfc7946) wird ein Rand zwischen zwei Punkten als gerade kartesische Linie definiert.
* Die Entscheidung, geodätische-oder planare Kanten zu verwenden, hängt möglicherweise vom DataSet ab und ist besonders in langen Kanten relevant.

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird eine Straße in der Manhattan-Insel bezeichnet. Der Rand ist kurz, und der Abstand zwischen dem planaren Rand und dem zugehörigen geodäc-Pendant ist kleiner als der von Tolerance angegebene Abstand. Das Ergebnis bleibt unverändert.

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[-73.949247, 40.796860],[-73.973017, 40.764323]]})))
```

|densified_line|
|---|
|{"Type": "LineString", "Koordinaten": [[-73,949247, 40,796860], [-73,973017, 40,764323]]}|

Im folgenden Beispiel wird eine Kante der Länge ~ 130km nicht identifiziert.

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[50, 50], [51, 51]]})))
```

|densified_line|
|---|
|{"Type": "LineString", "Koordinaten": [[50, 50], [50.125, 50.125], [50.25, 50.25], [50.375, 50.375], [50,5, 50,5], [50.625, 50.625], [50.75, 50.75], [50.875, 50.875], [51, 51]]}|

Im folgenden Beispiel wird aufgrund der ungültigen Koordinaten Eingabe ein NULL-Ergebnis zurückgegeben.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[300,1],[1,1]]}))
```

|densified_line|
|---|
||

Das folgende Beispiel gibt ein NULL-Ergebnis zurück, weil ungültige Toleranz Eingaben eingegeben wurden.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[1,1],[2,2]]}), 0)
```

|densified_line|
|---|
||
