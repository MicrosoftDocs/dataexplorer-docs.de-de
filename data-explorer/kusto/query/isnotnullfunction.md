---
title: IsNotNull ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird IsNotNull () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c198bd34161c683c6e5c6f1bde2990c0605122c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253271"
---
# <a name="isnotnull"></a>isnotnull()

Gibt zurück, `true` Wenn das Argument nicht NULL ist.

## <a name="syntax"></a>Syntax

`isnotnull(`[*Wert*]`)`

`notnull(`[*Wert*] `)` -Alias für `isnotnull`

## <a name="example"></a>Beispiel

```kusto
T | where isnotnull(PossiblyNull) | count
```

Beachten Sie, dass es andere Möglichkeiten gibt, diesen Effekt zu erreichen:

```kusto
T | summarize count(PossiblyNull)
```