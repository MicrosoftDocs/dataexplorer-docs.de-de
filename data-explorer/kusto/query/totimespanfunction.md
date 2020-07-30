---
title: "\"getimespan ()\": Azure-Daten-Explorer"
description: In diesem Artikel wird "in Azure Daten-Explorer" beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1edc5e3ef8c3c2dea65d332e6ceace653cc5c812
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340138"
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

## <a name="returns"></a>Gibt zurück

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis als [TimeSpan](./scalar-data-types/timespan.md) -Wert verwendet.
Andernfalls ist das Ergebnis NULL.
