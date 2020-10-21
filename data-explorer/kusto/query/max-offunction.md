---
title: max_of ()-Azure Daten-Explorer
description: In diesem Artikel wird max_of () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2a9cbb64ce1bbbc7d58b66c260d7968d8a60748
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248957"
---
# <a name="max_of"></a>max_of()

Gibt den maximalen Wert von mehreren ausgewerteten numerischen Ausdrücken zurück.

```kusto
max_of(10, 1, -3, 17) == 17
```

## <a name="syntax"></a>Syntax

`max_of``(` *expr_1* `,` *expr_2* ...`)`

## <a name="arguments"></a>Argumente

* *expr_i*: ein skalarer Ausdruck, der ausgewertet werden soll.

- Alle Argumente müssen denselben Typ aufweisen.
- Maximal 64 Argumente werden unterstützt.

## <a name="returns"></a>Rückgabe

Der maximale Wert aller Argument Ausdrücke.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
