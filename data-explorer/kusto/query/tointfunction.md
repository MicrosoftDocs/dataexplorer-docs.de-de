---
title: toint() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt toint() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570e13dc816c8a7e6d5baa488912fd8def5d2883
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506102"
---
# <a name="toint"></a>toint()

Konvertiert Die Eingabe in die ganzzahlige (signierte 32-Bit) Zahlendarstellung.

```kusto
toint("123") == 123
```

**Syntax**

`toint(`*Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der in ganze Zahl konvertiert wird. 

**Rückgabe**

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine ganze Zahl.
Wenn die Konvertierung nicht erfolgreich `null`ist, wird das Ergebnis .
 
*Hinweis*: Bevorzugen Sie die Verwendung von [int()](./scalar-data-types/int.md) wenn möglich.