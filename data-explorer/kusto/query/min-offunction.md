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
ms.openlocfilehash: 0762b1416df32279b9801c47f129a6966772a7e2
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271365"
---
# <a name="min_of"></a>min_of()

Gibt den minimalen Wert von mehreren ausgewerteten numerischen Ausdrücken zurück.

```kusto
min_of(10, 1, -3, 17) == -3
```

**Syntax**

`min_of``(` *expr_1* `,` *expr_2* ...`)`

**Argumente**

* *expr_i*: ein skalarer Ausdruck, der ausgewertet werden soll.

- Alle Argumente müssen denselben Typ aufweisen.
- Maximal 64 Argumente werden unterstützt.

**Rückgabe**

Der minimale Wert aller Argument Ausdrücke.

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
