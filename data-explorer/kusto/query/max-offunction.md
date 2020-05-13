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
ms.openlocfilehash: 4b912b1bdc68d7b3071ace8547f0aaf7c679a86a
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271603"
---
# <a name="max_of"></a>max_of()

Gibt den maximalen Wert von mehreren ausgewerteten numerischen Ausdrücken zurück.

```kusto
max_of(10, 1, -3, 17) == 17
```

**Syntax**

`max_of``(` *expr_1* `,` *expr_2* ...`)`

**Argumente**

* *expr_i*: ein skalarer Ausdruck, der ausgewertet werden soll.

- Alle Argumente müssen denselben Typ aufweisen.
- Maximal 64 Argumente werden unterstützt.

**Rückgabe**

Der maximale Wert aller Argument Ausdrücke.

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
