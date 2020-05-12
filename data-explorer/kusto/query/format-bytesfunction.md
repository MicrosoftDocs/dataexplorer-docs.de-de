---
title: format_bytes ()-Azure Daten-Explorer
description: In diesem Artikel wird format_bytes () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4da07433be5a052d71740931d4dedd9df0399f56
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227320"
---
# <a name="format_bytes"></a>format_bytes()

Formatiert eine Zahl als Zeichenfolge, die die Datengröße in Bytes darstellt.

```kusto
format_bytes(1024) == '1 KB'"
```

**Syntax**

`format_bytes(`*Wert* [ `,` *Precision* [ `,` *Einheiten*]]`)`

**Argumente**

* `value`: eine Zahl, die als Datengröße in Bytes formatiert werden soll.
* `precision`: (optional) Anzahl der Ziffern, auf die der Wert gerundet wird. (der Standardwert ist 0).
* `units`: (optional) Einheiten der Ziel Datengröße, die von der Zeichen folgen Formatierung verwendet werden ( `Bytes` , `KB` , `MB` , `GB` , `TB` , `PB` ). Wenn der Parameter leer ist, werden die Einheiten basierend auf dem Eingabe Wert automatisch ausgewählt.

**Rückgabe**

Die Zeichenfolge mit dem Format Ergebnis.

**Beispiele**

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
