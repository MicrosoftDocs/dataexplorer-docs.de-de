---
title: binary_and ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_and () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93318785cff98c7ca024b638e5e90f58cd9cd9d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249365"
---
# <a name="binary_and"></a>binary_and()

Gibt das Ergebnis der bitweisen `and` Operation zwischen zwei Werten zurück.

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>Syntax

`binary_and(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Argumente

* *num1*, *num2*: lange Zahlen.

## <a name="returns"></a>Rückgabe

Gibt eine logische and-Operation für ein Zahlenpaar zurück: num1 & num2.