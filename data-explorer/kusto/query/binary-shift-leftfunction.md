---
title: binary_shift_left ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_shift_left () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f01f0178fa850009f6298446b02c9d4bd913bf8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252612"
---
# <a name="binary_shift_left"></a>binary_shift_left()

Gibt den binären Shift Left-Vorgang für ein Zahlenpaar zurück.

```kusto
binary_shift_left(x,y)  
```

## <a name="syntax"></a>Syntax

`binary_shift_left(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Argumente

* *num1*, *num2*: int-Nummern.

## <a name="returns"></a>Rückgabe

Gibt den binären Shift Left-Vorgang für ein Zahlenpaar zurück: num1 <<  (num2% 64).
Wenn n negativ ist, wird ein NULL-Wert zurückgegeben.