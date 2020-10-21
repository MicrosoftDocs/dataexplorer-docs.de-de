---
title: ToGuid ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird ToGuid () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d122833f4797c8503dd41cc8ba861554d6924338
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250390"
---
# <a name="toguid"></a>toguid()

Konvertiert Eingaben in eine- [`guid`](./scalar-data-types/guid.md) Darstellung.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> Verwenden Sie nach Möglichkeit die Verwendung von [GUID ()](./scalar-data-types/guid.md) .

## <a name="syntax"></a>Syntax

`toguid(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in einen skalaren Wert konvertiert wird [`guid`](./scalar-data-types/guid.md) . 

## <a name="returns"></a>Rückgabe

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [`guid`](./scalar-data-types/guid.md) Skalar.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
