---
title: Grad ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden Grad () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41d679ea1add3706de5012f4e4fbf382e1f7b3ee
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348376"
---
# <a name="degrees"></a>degrees()

Konvertiert den Winkelwert im Bogenmaß mit der Formel.`degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>Syntax

`degrees(`*ein*`)`

## <a name="arguments"></a>Argumente

* *a*: Winkel im Bogenmaße (eine reelle Zahl).

## <a name="returns"></a>Gibt zurück

* Der entsprechende Winkel in Grad für einen im Bogenmaß angegebenen Winkel. 

## <a name="examples"></a>Beispiele

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
