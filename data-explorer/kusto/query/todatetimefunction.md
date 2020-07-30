---
title: DateTime ()-Azure-Daten-Explorer
description: In diesem Artikel wird "DateTime ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6a991f9f64decbb3985830cf2611361d6c66db31
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350841"
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
