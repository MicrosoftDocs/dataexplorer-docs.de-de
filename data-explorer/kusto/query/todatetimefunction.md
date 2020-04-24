---
title: todatetime() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt todatetime() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe3852195b1a79ab5c86176698099ed6e7ff7af
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506340"
---
# <a name="todatetime"></a>todatetime()

Konvertiert Die Eingabe in [datetime](./scalar-data-types/datetime.md) skalar.

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**Syntax**

`todatetime(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in [datetime](./scalar-data-types/datetime.md)konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [Datetime-Wert.](./scalar-data-types/datetime.md)
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis null.
 
*Hinweis*: Bevorzugen Sie die Verwendung von [datetime()](./scalar-data-types/datetime.md) wenn möglich.