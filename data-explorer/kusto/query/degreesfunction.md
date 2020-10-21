---
title: Grad ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden Grad () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 04efbee59bce153de76ab5d44617b8d1bebc121c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253094"
---
# <a name="degrees"></a>degrees()

Konvertiert den Winkelwert im Bogenmaß mit der Formel. `degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>Syntax

`degrees(`*ein*`)`

## <a name="arguments"></a>Argumente

* *a*: Winkel im Bogenmaße (eine reelle Zahl).

## <a name="returns"></a>Rückgabe

* Der entsprechende Winkel in Grad für einen im Bogenmaß angegebenen Winkel. 

## <a name="examples"></a>Beispiele

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
