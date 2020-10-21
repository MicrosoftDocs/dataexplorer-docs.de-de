---
title: radiane ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden radiane () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bacb005b8828c63efac1737c527cc313a3ee052b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241318"
---
# <a name="radians"></a>radians()

Konvertiert den Winkelwert in Grad in den Wert im Bogenmaß mithilfe der Formel. `radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>Syntax

`radians(`*ein*`)`

## <a name="arguments"></a>Argumente

* *a*: Winkel in Grad (eine reelle Zahl).

## <a name="returns"></a>Rückgabe

* Der entsprechende Winkel im Bogenmaß für einen in Grad angegebenen Winkel. 

## <a name="examples"></a>Beispiele

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1,5707963267949|3,14159265358979|6.28318530717959|