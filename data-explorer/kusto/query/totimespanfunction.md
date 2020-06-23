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
ms.openlocfilehash: b785f346dd95a9c6a8cb9d6148e889c42ac4b02c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129038"
---
# <a name="totimespan"></a>totimespan()

Konvertiert Eingaben in [TimeSpan](./scalar-data-types/timespan.md) Scalar.

```kusto
totimespan("0.00:01:00") == time(1min)
```

**Syntax**

`totimespan(Expr)`

**Argumente**

* *`Expr`*: Ausdruck, der in [TimeSpan](./scalar-data-types/timespan.md)konvertiert wird.

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis als [TimeSpan](./scalar-data-types/timespan.md) -Wert verwendet.
Andernfalls ist das Ergebnis NULL.
