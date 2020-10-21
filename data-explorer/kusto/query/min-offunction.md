---
title: min_of ()-Azure Daten-Explorer
description: In diesem Artikel wird min_of () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c08ef6f147640330cd5ea33c55dcc6acafd77a31
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248814"
---
# <a name="min_of"></a>min_of()

Gibt den minimalen Wert von mehreren ausgewerteten numerischen Ausdrücken zurück.

```kusto
min_of(10, 1, -3, 17) == -3
```

## <a name="syntax"></a>Syntax

`min_of``(` *expr_1* `,` *expr_2* ...`)`

## <a name="arguments"></a>Argumente

* *expr_i*: ein skalarer Ausdruck, der ausgewertet werden soll.

- Alle Argumente müssen denselben Typ aufweisen.
- Maximal 64 Argumente werden unterstützt.

## <a name="returns"></a>Rückgabe

Der minimale Wert aller Argument Ausdrücke.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
