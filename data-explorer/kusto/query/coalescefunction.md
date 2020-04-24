---
title: coalesce() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird coalesce() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ee2cd24f36007914fdc326e2863da148aec4406
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517135"
---
# <a name="coalesce"></a>coalesce()

Wertet eine Liste von Ausdrücken aus und gibt den ersten Ausdruck nicht NULL (oder nicht leer für Zeichenfolgen) zurück.

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**Syntax**

`coalesce(`*expr_1*`, `*expr_2* `,` ...)

**Argumente**

* *expr_i*: Ein skalarer Ausdruck, der ausgewertet werden soll.
- Alle Argumente müssen vom gleichen Typ sein.
- Maximal 64 Argumente werden unterstützt.


**Rückgabe**

Der Wert der ersten *expr_i* deren Wert nicht null (oder nicht leer für Zeichenfolgenausdrücke) ist.

**Beispiel**

```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|