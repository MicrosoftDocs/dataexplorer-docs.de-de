---
title: binary_xor ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_xor () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aecf005556a9997e63ee3547f4e0b23da236cf5d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243459"
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