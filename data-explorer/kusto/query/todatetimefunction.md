---
title: DateTime ()-Azure-Daten-Explorer
description: In diesem Artikel wird "DateTime ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b9248ddc76a4893cf1edd353c0fbd036c9a1e7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250537"
---
# <a name="todatetime"></a>todatetime()

Konvertiert die Eingabe in einen [DateTime](./scalar-data-types/datetime.md) -skalaren Wert.

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

## <a name="syntax"></a>Syntax

`todatetime(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in [DateTime](./scalar-data-types/datetime.md)konvertiert wird.

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [DateTime](./scalar-data-types/datetime.md) -Wert.
Andernfalls ist das Ergebnis NULL.
 
> [!NOTE]
> Verwenden Sie nach Möglichkeit die Verwendung von [DateTime ()](./scalar-data-types/datetime.md) .
