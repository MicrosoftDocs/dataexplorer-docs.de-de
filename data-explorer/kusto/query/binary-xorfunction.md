---
title: binary_xor ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_xor () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f988fa3d14dab3188bf96825615972995291655
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349005"
---
# <a name="binary_xor"></a>binary_xor()

Gibt das Ergebnis der bitweisen- `xor` Operation der beiden-Werte zurück.

```kusto
binary_xor(x,y)
```

## <a name="syntax"></a>Syntax

`binary_xor(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Argumente

* *num1*, *num2*: lange Zahlen.

## <a name="returns"></a>Rückgabe

Gibt eine logische XOR-Operation für ein Zahlenpaar zurück: num1 ^ num2.