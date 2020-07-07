---
title: "\"zu\" (): Azure-Daten-Explorer"
description: In diesem Artikel wird "$ int ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1efd06b8fa2961528be65b630933aae74614e9a0
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013605"
---
# <a name="toint"></a>toint()

Konvertiert Eingaben in ganzzahlige (signierte 32-Bit-) Zahlen Darstellungen.

```kusto
toint("123") == int(123)
```

**Syntax**

`toint(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in eine ganze Zahl konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine ganze Zahl.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .
 
*Hinweis*: Verwenden Sie möglichst [int ()](./scalar-data-types/int.md) .
