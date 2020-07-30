---
title: radiane ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden radiane () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3aaa41a631498e2938acf722b75f409a1bbe5031
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345945"
---
# <a name="radians"></a>radians()

Konvertiert den Winkelwert in Grad in den Wert im Bogenmaß mithilfe der Formel.`radians = (PI / 180 ) * angle_in_degrees`

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