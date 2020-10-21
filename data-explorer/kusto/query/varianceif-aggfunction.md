---
title: varianceif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird varianceif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 71c80afa5a2cfe79fb17aeb610f62c3076dd5f84
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245587"
---
# <a name="varianceif-aggregation-function"></a>varianceif () (Aggregations Funktion)

Berechnet die [Varianz](variance-aggfunction.md) von *expr* in der Gruppe, für die das *Prädikat* ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`varianceif(` *expr*- `, ` *Prädikat* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 
* *Prädikat*: Prädikat: Wenn true, wird der berechnete *expr* -Wert der Varianz hinzugefügt.

## <a name="returns"></a>Rückgabe

Der Varianz Wert von *expr* für die Gruppe, in der das *Prädikat* ausgewertet wird `true` .
 
## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|