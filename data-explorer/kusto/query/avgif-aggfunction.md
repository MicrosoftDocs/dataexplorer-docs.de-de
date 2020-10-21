---
title: avgif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird avgif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e0d112554ff77cb9c591f787bbb62f9a92e3b19d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248014"
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