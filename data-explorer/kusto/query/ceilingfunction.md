---
title: Ceiling ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Ceiling () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e2a29d28b25d26d582aa49717d5ce5576276f450
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348903"
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