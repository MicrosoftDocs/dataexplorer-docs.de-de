---
title: abschlüsse() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden degrees() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1365d6c6629f4f4769d7c4b62491eaec25e4ec59
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516030"
---
# <a name="degrees"></a>degrees()

Konvertiert den Winkelwert in Bogenmaß in Wert in Grad, mit Formel`degrees = (180 / PI ) * angle_in_radians`

**Syntax**

`degrees(`*Eine*`)`

**Argumente**

* *a*: Winkel in Bogenmaß (eine reelle Zahl).

**Rückgabe**

* Der entsprechende Winkel in Grad für einen in Bogenmaß angegebenen Winkel. 

**Beispiele**

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|Grad0|Grad1|Grad2|
|---|---|---|
|45|270|0|
