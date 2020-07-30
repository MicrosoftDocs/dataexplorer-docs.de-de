---
title: binary_and ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird binary_and () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 611580aebbfd974377f5a22ec904bbbcdbeb6e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349090"
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