---
title: format_bytes ()-Azure Daten-Explorer
description: In diesem Artikel wird format_bytes () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c324813ed53b57673f8962f87374eb998f2a3443
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244757"
---
# <a name="format_bytes"></a>format_bytes()

Formatiert eine Zahl als Zeichenfolge, die die Datengröße in Bytes darstellt.

```kusto
format_bytes(1024) == '1 KB'"
```

## <a name="syntax"></a>Syntax

`format_bytes(`*Wert* [ `,` *Precision* [ `,` *Einheiten*]]`)`

## <a name="arguments"></a>Argumente

* `value`: eine Zahl, die als Datengröße in Bytes formatiert werden soll.
* `precision`: (optional) Anzahl der Ziffern, auf die der Wert gerundet wird. (der Standardwert ist 0).
* `units`: (optional) Einheiten der Ziel Datengröße, die von der Zeichen folgen Formatierung verwendet werden ( `Bytes` , `KB` , `MB` , `GB` , `TB` , `PB` ). Wenn der Parameter leer ist, werden die Einheiten basierend auf dem Eingabe Wert automatisch ausgewählt.

## <a name="returns"></a>Rückgabe

Die Zeichenfolge mit dem Format Ergebnis.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|V2|V3|v4|V5|
|---|---|---|---|---|
|564 bytes|10,1 KB|19 MB|19,08 MB|19541 KB|
