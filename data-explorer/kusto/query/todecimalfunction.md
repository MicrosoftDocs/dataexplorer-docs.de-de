---
title: $ Decimal ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird das-Zeichen () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4b9f020a1d37b7279c0712951ed0c2e39e9e428
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250494"
---
# <a name="todecimal"></a>todecimal()

Konvertiert Eingaben in eine Dezimalzahl Darstellung.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>Syntax

`todecimal(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in einen Dezimalwert konvertiert wird. 

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis eine Dezimalzahl sein.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
 
*Hinweis*: Verwenden Sie möglichst [Real ()](./scalar-data-types/real.md) .