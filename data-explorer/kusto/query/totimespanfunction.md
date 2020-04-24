---
title: totimespan() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt totimespan() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 504d4a74e8c1b58a8a97fd80d6c846fcf7e3f527
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505864"
---
# <a name="totimespan"></a>totimespan()

Konvertiert Die Eingabe in [Zeitspanskala](./scalar-data-types/timespan.md) skalar.

```kusto
totimespan("0.00:01:00") == time(1min)
```

**Syntax**

`totimespan(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in [Zeitspanne](./scalar-data-types/timespan.md)konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [Zeitzeitraumwert.](./scalar-data-types/timespan.md)
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis null.
 