---
title: avgif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird avgif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 587af53de774332db70ef9bffcadf74d9e2c069d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349379"
---
# <a name="avgif-aggregation-function"></a>avgif () (Aggregations Funktion)

Berechnet den [Durchschnitt](avg-aggfunction.md) von *expr* in der Gruppe, für die das *Prädikat* ausgewertet wird `true` .

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md) Fassung verwendet werden.

## <a name="syntax"></a>Syntax

`avgif(` *expr*- `, ` *Prädikat* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. Datensätze mit `null` Werten werden ignoriert und nicht in die Berechnung eingeschlossen.
* *Prädikat*: Prädikat: Wenn true, wird der berechnete *expr* -Wert dem Durchschnitt hinzugefügt.

## <a name="returns"></a>Rückgabe

Der Durchschnittswert von *expr* in der Gruppe, in der das *Prädikat* ergibt `true` .
 
## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|