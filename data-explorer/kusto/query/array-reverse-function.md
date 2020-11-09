---
title: array_reverse ()-Azure Daten-Explorer
description: In diesem Artikel wird array_reverse () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 327564a953c8f537a0f76727b4313749f61eec3e
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94374005"
---
# <a name="array_reverse"></a>array_reverse ()

Kehrt die Reihenfolge der Elemente in einem dynamischen Array um.

## <a name="syntax"></a>Syntax

`array_reverse(`*array*`)`

## <a name="arguments"></a>Argumente

*Array* : Eingabe Array in umgekehrter Reihenfolge.

## <a name="returns"></a>Gibt zurück

Ein Array, das genau dieselben Elemente wie das Eingabe Array enthält, jedoch in umgekehrter Reihenfolge.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_reverse(arr)
```

|Ergebnis|
|---|
|["example", "an", "is", "This"]|
