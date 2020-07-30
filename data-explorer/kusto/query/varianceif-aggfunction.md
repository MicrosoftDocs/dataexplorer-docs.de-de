---
title: varianceif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird varianceif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf1009d2d269bf21ea5ae14a9c828724d8bf8c70
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338472"
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