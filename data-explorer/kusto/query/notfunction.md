---
title: not() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt not() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 539e409a9e922afc390b097c863146b7fc30d7b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512035"
---
# <a name="not"></a>not()

Kehrt den Wert `bool` seines Arguments um.

```kusto
not(false) == true
```

**Syntax**

`not(`*Expr*`)`

**Argumente**

* *expr*: `bool` Ein Ausdruck, der umgekehrt werden soll.

**Rückgabe**

Gibt den umgekehrten `bool` logischen Wert des Arguments zurück.