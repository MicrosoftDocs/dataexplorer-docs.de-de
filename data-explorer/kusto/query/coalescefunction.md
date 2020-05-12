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
ms.openlocfilehash: ea57efe36fb86189d798e5f18fa3fe9470bfd634
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227535"
---
# <a name="coalesce"></a>coalesce()

Wertet eine Liste von Ausdrücken aus und gibt den ersten Ausdruck zurück, der nicht NULL ist (oder nicht leer für eine Zeichenfolge).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**Syntax**

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

**Argumente**

* *expr_i*: ein skalarer Ausdruck, der ausgewertet werden soll.
- Alle Argumente müssen denselben Typ aufweisen.
- Maximal 64 Argumente werden unterstützt.


**Rückgabe**

Der Wert des ersten *expr_i* , dessen Wert nicht NULL ist (oder für Zeichen folgen Ausdrücke nicht leer ist).

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
