---
title: Ceiling ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Ceiling () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b307121e102229edbe62c4d4dd7f910ea2ce8756
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249328"
---
# <a name="ceiling"></a>ceiling()

Berechnet die kleinste Ganzzahl, die größer oder gleich dem angegebenen numerischen Ausdruck ist.

## <a name="syntax"></a>Syntax

`ceiling(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl.

## <a name="returns"></a>Rückgabe

* Die kleinste ganze Zahl, die größer oder gleich dem angegebenen numerischen Ausdruck ist. 

## <a name="examples"></a>Beispiele

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|