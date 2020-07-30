---
title: 'stdevif () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird stdevif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a158a623768a7beb6ec497ca8d8467aecd7c3b61
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342817"
---
# <a name="stdevif-aggregation-function"></a>stdevif () (Aggregations Funktion)

Berechnet die [StDev](stdev-aggfunction.md) von *expr* in der Gruppe, für die das *Prädikat* ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`stdevif(` *expr*- `, ` *Prädikat* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 
* *Prädikat*: Prädikat: Wenn true, wird der berechnete *expr* -Wert der Standardabweichung hinzugefügt.

## <a name="returns"></a>Rückgabe

Der Standardwert für die Abweichung von *expr* für die Gruppe, in der das *Prädikat* ausgewertet wird `true` .
 
## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|