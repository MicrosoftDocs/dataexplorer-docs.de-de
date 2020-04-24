---
title: radians() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Radander() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0dc04c5f9593b6bd5fc61f57d20819cf7d2a178c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510658"
---
# <a name="radians"></a>radians()

Konvertiert Winkelwert in Grad in Wert in Bogenmaß, mit Formel`radians = (PI / 180 ) * angle_in_degrees`

**Syntax**

`radians(`*Eine*`)`

**Argumente**

* *a*: Winkel in Grad (eine reelle Zahl).

**Rückgabe**

* Der entsprechende Winkel in Bogenmaß für einen in Grad angegebenen Winkel. 

**Beispiele**

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|Radians1|radians2|
|---|---|---|
|1.5707963267949|3.14159265358979|6.28318530717959|