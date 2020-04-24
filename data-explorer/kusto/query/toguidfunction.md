---
title: toguid() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt toguid() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb1df5fe91b75e5d3b7d1a9f40b8b3079cac9944
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506136"
---
# <a name="toguid"></a>toguid()

Konvertiert Eingabe [`guid`](./scalar-data-types/guid.md) in Repräsentation.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

**Syntax**

`toguid(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der [`guid`](./scalar-data-types/guid.md) in Skalar konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, wird das Ergebnis ein [`guid`](./scalar-data-types/guid.md) Skalar sein.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .

*Hinweis*: Bevorzugen Sie die Verwendung von [guid()](./scalar-data-types/guid.md) wenn möglich.