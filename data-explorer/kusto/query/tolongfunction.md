---
title: Tolong ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Tolong () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36c0317f046f146d2812b8830d7fe571d5363c59
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804116"
---
# <a name="tolong"></a>tolong()

Konvertiert Eingaben in eine Long-Darstellung (signed 64-Bit).

```kusto
tolong("123") == 123
```

> [!NOTE]
> Verwenden Sie nach Möglichkeit [Long ()](./scalar-data-types/long.md) .

## <a name="syntax"></a>Syntax

`tolong(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in Long konvertiert wird. 

## <a name="returns"></a>Gibt zurück

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine lange Zahl sein.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
 