---
title: "\"getimespan ()\": Azure-Daten-Explorer"
description: In diesem Artikel wird "in Azure Daten-Explorer" beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0779a6260cc87f8a602f4751d28c33de9bacb57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243745"
---
# <a name="totimespan"></a>totimespan()

Konvertiert Eingaben in [TimeSpan](./scalar-data-types/timespan.md) Scalar.

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>Syntax

`totimespan(Expr)`

## <a name="arguments"></a>Argumente

* *`Expr`*: Ausdruck, der in [TimeSpan](./scalar-data-types/timespan.md)konvertiert wird.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis als [TimeSpan](./scalar-data-types/timespan.md) -Wert verwendet.
Andernfalls ist das Ergebnis NULL.
