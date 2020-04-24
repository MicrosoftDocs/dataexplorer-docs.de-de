---
title: min_of() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird min_of() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06a8f7ce6bcef8f3c15c4ea3d4c997b4e4540bf7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512392"
---
# <a name="min_of"></a>min_of()

Gibt den Mindestwert mehrerer ausgewerteter numerischer Ausdrücke zurück.

```kusto
min_of(10, 1, -3, 17) == -3
```

**Syntax**

`min_of``(` *expr_1* expr_1`,` *expr_2* ...`)`

**Argumente**

* *expr_i*: Ein skalarer Ausdruck, der ausgewertet werden soll.

- Alle Argumente müssen vom gleichen Typ sein.
- Maximal 64 Argumente werden unterstützt.

**Rückgabe**

Der Mindestwert aller Argumentausdrücke.

**Beispiel**

```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|