---
title: binary_or ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_or () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 97144b244fb6fea5ac218f6160d8aa9e95f50aa4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247889"
---
# <a name="binary_or"></a>binary_or()

Gibt das Ergebnis der bitweisen- `or` Operation der beiden-Werte zurück. 

```kusto
binary_or(x,y)
```

## <a name="syntax"></a>Syntax

`binary_or(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Argumente

* *num1*, *num2*: lange Zahlen.

## <a name="returns"></a>Rückgabe

Gibt eine logische OR-Operation für ein Zahlenpaar zurück: num1 | num2.