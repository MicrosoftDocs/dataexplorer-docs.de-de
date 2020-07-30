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
ms.openlocfilehash: fb209ff3784f6e24f184b576a1e8f94c52834ba4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340954"
---
# <a name="tolong"></a>tolong()

Konvertiert Eingaben in eine Long-Darstellung (signed 64-Bit).

```kusto
tolong("123") == 123
```

## <a name="syntax"></a>Syntax

`tolong(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in Long konvertiert wird. 

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine lange Zahl sein.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
 
*Hinweis*: Verwenden Sie nach Möglichkeit [Long ()](./scalar-data-types/long.md) .