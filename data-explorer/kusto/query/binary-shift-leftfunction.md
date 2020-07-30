---
title: binary_shift_left ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_shift_left () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 46d18bb5d1f661c5346f5ff825c9597088d3f5cf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349039"
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