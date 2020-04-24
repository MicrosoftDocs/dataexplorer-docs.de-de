---
title: format_bytes() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird format_bytes() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5d5bfa234e001f498737b9df88274096f8592f52
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515163"
---
# <a name="format_bytes"></a>format_bytes()

Formatiert eine Zahl als Zeichenfolge, die die Datengröße in Bytes darstellt.

```kusto
format_bytes(1024) == '1 KB'"
```

**Syntax**

`format_bytes(`*Wert* `,` *precision* [`,` Genauigkeit [ *Einheiten*]]`)`

**Argumente**

* `value`: eine Zahl, die als Datengröße in Bytes formatiert werden soll.
* `precision`: (optional) Anzahl der Ziffern, auf die der Wert gerundet wird. (Standardwert ist 0).
* `units`: (optional) Einheiten der Zieldatengröße, die`Bytes` `KB`die `MB` `GB`Zeichenfolgenformatierung verwendet ( , , , , `TB`, . `PB` Wenn der Parameter leer ist, werden die Einheiten basierend auf dem Eingabewert automatisch ausgewählt.

**Rückgabe**

Die Zeichenfolge mit dem Formatergebnis.

**Beispiele**

```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|v1|V2|V3|v4|v5|
|---|---|---|---|---|
|564 Bytes|10,1 KB|19 MB|19.08 MB|19541 KB|