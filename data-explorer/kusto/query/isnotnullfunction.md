---
title: IsNotNull ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IsNotNull () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ff472919ecda9550e7e0bcd6b403c605d029bfb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347186"
---
# <a name="isnotnull"></a>isnotnull()

Gibt zurück, `true` Wenn das Argument nicht NULL ist.

## <a name="syntax"></a>Syntax

`isnotnull(`[*Wert*]`)`

`notnull(`[*Wert*] `)` -Alias für`isnotnull`

## <a name="example"></a>Beispiel

```kusto
T | where isnotnull(PossiblyNull) | count
```

Beachten Sie, dass es andere Möglichkeiten gibt, diesen Effekt zu erreichen:

```kusto
T | summarize count(PossiblyNull)
```