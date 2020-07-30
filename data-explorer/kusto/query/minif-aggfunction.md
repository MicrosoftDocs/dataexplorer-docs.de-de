---
title: 'minif () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird minif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91764aeb8c825a272c414df7a0572d3b8310e79f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346744"
---
# <a name="minif-aggregation-function"></a>minif () (Aggregations Funktion)

Gibt den minimalen Wert für die Gruppe zurück, für den das *Prädikat* ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

Siehe auch- [Min ()](min-aggfunction.md) -Funktion, die den minimalen Wert für die Gruppe ohne Prädikat Ausdruck zurückgibt.

## <a name="syntax"></a>Syntax

`summarize``minif(` *Expr*- `,` *Prädikat*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird.
* *Prädikat*: Prädikat: Wenn der Wert true ist, wird der berechnete *expr* -Wert minimal geprüft.

## <a name="returns"></a>Rückgabe

Der minimale Wert von *expr* in der Gruppe, für die das *Prädikat* ausgewertet wird `true` .

## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|