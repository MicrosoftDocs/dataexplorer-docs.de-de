---
title: binary_shift_right ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_shift_right () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 96da8894aa4320a2d423d072acc048994463a7b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349022"
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