---
title: max_of() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird max_of() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68188ccd5eb814a22be166b8847d80193172813f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512494"
---
# <a name="max_of"></a>max_of()

Gibt den Maximalwert mehrerer ausgewerteter numerischer Ausdrücke zurück.

```kusto
max_of(10, 1, -3, 17) == 17
```

**Syntax**

`max_of``(` *expr_1* expr_1`,` *expr_2* ...`)`

**Argumente**

* *expr_i*: Ein skalarer Ausdruck, der ausgewertet werden soll.

- Alle Argumente müssen vom gleichen Typ sein.
- Maximal 64 Argumente werden unterstützt.

**Rückgabe**

Der maximale Wert aller Argumentausdrücke.

**Beispiel**

```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|