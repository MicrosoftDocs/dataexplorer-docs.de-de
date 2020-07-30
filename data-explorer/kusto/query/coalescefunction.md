---
title: COALESCE ()-Azure-Daten-Explorer
description: In diesem Artikel wird COALESCE () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 410a0c84a1bafdfa1900ef8e21bc0a91327b64c3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348869"
---
# <a name="coalesce"></a>coalesce()

Wertet eine Liste von Ausdrücken aus und gibt den ersten Ausdruck zurück, der nicht NULL ist (oder nicht leer für eine Zeichenfolge).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

## <a name="syntax"></a>Syntax

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

## <a name="arguments"></a>Argumente

* *expr_i*: ein skalarer Ausdruck, der ausgewertet werden soll.
- Alle Argumente müssen denselben Typ aufweisen.
- Maximal 64 Argumente werden unterstützt.


## <a name="returns"></a>Rückgabe

Der Wert des ersten *expr_i* , dessen Wert nicht NULL ist (oder für Zeichen folgen Ausdrücke nicht leer ist).

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
