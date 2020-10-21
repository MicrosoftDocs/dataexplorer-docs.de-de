---
title: binary_shift_right ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_shift_right () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d33ecb954a7e1e6d0c9c39bdbf057d284affd22b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243523"
---
# <a name="binary_shift_right"></a>binary_shift_right()

Gibt den binären Verschiebungs rechten Vorgang für ein Zahlenpaar zurück.

```kusto
binary_shift_right(x,y) 
```

## <a name="syntax"></a>Syntax

`binary_shift_right(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Argumente

* *num1*, *num2*: lange Zahlen.

## <a name="returns"></a>Rückgabe

Gibt den binären shift right-Vorgang für ein Zahlenpaar zurück: num1 >>  (num2% 64).
Wenn n negativ ist, wird ein NULL-Wert zurückgegeben.