---
title: max_of ()-Azure Daten-Explorer
description: In diesem Artikel wird max_of () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2045436de09bc31fa0378824310fa872478b861
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346829"
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
