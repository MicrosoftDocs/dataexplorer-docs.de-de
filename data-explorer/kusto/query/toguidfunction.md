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
ms.openlocfilehash: a2eec6a582c4c8fc6cda6b3cf9a304f41ab48143
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350728"
---
# <a name="toguid"></a>toguid()

Konvertiert Eingaben in eine- [`guid`](./scalar-data-types/guid.md) Darstellung.

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

## <a name="syntax"></a>Syntax

`toguid(`*Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der in einen skalaren Wert konvertiert wird [`guid`](./scalar-data-types/guid.md) . 

## <a name="returns"></a>Gibt zurück

Wenn die Konvertierung erfolgreich ist, ist das Ergebnis ein [`guid`](./scalar-data-types/guid.md) Skalar.
Wenn die Konvertierung nicht erfolgreich ist, wird das Ergebnis zurückgeben `null` .

*Hinweis*: Verwenden Sie nach Möglichkeit die Verwendung von [GUID ()](./scalar-data-types/guid.md) .