---
title: ToGuid ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird ToGuid () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 689ee6bf7b3fcb27dced20b06a9002659622902e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804133"
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

## <a name="returns"></a>Gibt zurück

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [`guid`](./scalar-data-types/guid.md) Skalar.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .
