---
title: "\"zu\" (): Azure-Daten-Explorer"
description: In diesem Artikel wird "$ int ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7f0ede908be2689165f641038b2b6f699c0eb543
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550604"
---
# <a name="toint"></a>toint()

Konvertiert Eingaben in ganzzahlige (signierte 32-Bit-) Zahlen Darstellungen.

```kusto
toint("123") == 123s
```

**Syntax**

`toint(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in eine ganze Zahl konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine ganze Zahl.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .
 
*Hinweis*: Verwenden Sie möglichst [int ()](./scalar-data-types/int.md) .