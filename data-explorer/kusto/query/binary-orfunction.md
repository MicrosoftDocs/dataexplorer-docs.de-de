---
title: binary_or ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_or () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6903ee36e29551e7af6d08e686c1189e0c0125f3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349056"
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