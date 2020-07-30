---
title: min_of ()-Azure Daten-Explorer
description: In diesem Artikel wird min_of () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ed8d14a4e793f253342c1b52269678874c96660f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346761"
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

## <a name="returns"></a>Gibt zurück

Der minimale Wert aller Argument Ausdrücke.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
