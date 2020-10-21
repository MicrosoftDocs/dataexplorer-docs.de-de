---
title: Tolong ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Tolong () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf4960ae3bd11697e4e7203438e1a33af6c90672
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240999"
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

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine lange Zahl sein.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
 