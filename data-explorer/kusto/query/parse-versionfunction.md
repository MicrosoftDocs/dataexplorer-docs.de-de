---
title: parse_version() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_version() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5cb35c12849568f24a6bde42461e8af66058f48f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511712"
---
# <a name="parse_version"></a>parse_version()

Konvertiert die Darstellung der Eingabezeichenfolge der Version in eine vergleichbare Dezimalzahl.

```kusto
parse_version("0.0.0.1")
```

**Syntax**

`parse_version``(` *Expr*`)`

**Argumente**

* *Expr*: Ein skalarer `string` Ausdruck des Typs, der die zu analysierende Version angibt.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine Dezimalstelle sein.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .

**Hinweise**

Die Eingabezeichenfolge muss 1 bis 4 Versionsteile enthalten, als Zahlen dargestellt und durch Punkte ('.') getrennt.

Jeder Teil der Version kann bis zu 8 Ziffern (max. Wert - 99999999) enthalten.

Wenn die Teilemenge kleiner als 4 ist, werden alle fehlenden`1.0` == `1.0.0.0`Teile als nachgestellt betrachtet ( ).

 
**Beispiel**
```kusto
let dt = datatable(v:string)
["0.0.0.5","0.0.7.0","0.0.3","0.2","0.1.2.0","1.2.3.4","1","99999999.0.0.0"];
dt | project v1=v, _key=1 
| join kind=inner (dt | project v2=v, _key = 1) on _key | where v1 != v2
| summarize v1 = max(v1),v2 = min(v2) by (hash(v1) + hash(v2)) // removing duplications
| project v1, v2, higher_version = iif(parse_version(v1) > parse_version(v2), v1, v2)

```

|v1|V2|higher_version|
|---|---|---|
|99999999.0.0.0|0.0.0.5|99999999.0.0.0|
|1|0.0.0.5|1|
|1.2.3.4|0.0.0.5|1.2.3.4|
|0.1.2.0|0.0.0.5|0.1.2.0|
|0.2|0.0.0.5|0.2|
|0.0.3|0.0.0.5|0.0.3|
|0.0.7.0|0.0.0.5|0.0.7.0|
|99999999.0.0.0|0.0.7.0|99999999.0.0.0|
|1|0.0.7.0|1|
|1.2.3.4|0.0.7.0|1.2.3.4|
|0.1.2.0|0.0.7.0|0.1.2.0|
|0.2|0.0.7.0|0.2|
|0.0.7.0|0.0.3|0.0.7.0|
|99999999.0.0.0|0.0.3|99999999.0.0.0|
|1|0.0.3|1|
|1.2.3.4|0.0.3|1.2.3.4|
|0.1.2.0|0.0.3|0.1.2.0|
|0.2|0.0.3|0.2|
|99999999.0.0.0|0.2|99999999.0.0.0|
|1|0.2|1|
|1.2.3.4|0.2|1.2.3.4|
|0.2|0.1.2.0|0.2|
|99999999.0.0.0|0.1.2.0|99999999.0.0.0|
|1|0.1.2.0|1|
|1.2.3.4|0.1.2.0|1.2.3.4|
|99999999.0.0.0|1.2.3.4|99999999.0.0.0|
|1.2.3.4|1|1.2.3.4|
|99999999.0.0.0|1|99999999.0.0.0|




