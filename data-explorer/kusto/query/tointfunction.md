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
ms.openlocfilehash: 2daea4d190ed349c41a8eecf2eef53b2c2b93716
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350688"
---
# <a name="toint"></a>toint()

Konvertiert Eingaben in ganzzahlige (signierte 32-Bit-) Zahlen Darstellungen.

```kusto
toint("123") == int(123)
```

## <a name="syntax"></a>Syntax

`toint(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in eine ganze Zahl konvertiert wird. 

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis eine ganze Zahl.
Wenn die Konvertierung nicht erfolgreich ist, lautet das Ergebnis `null` .
 
*Hinweis*: Verwenden Sie möglichst [int ()](./scalar-data-types/int.md) .
