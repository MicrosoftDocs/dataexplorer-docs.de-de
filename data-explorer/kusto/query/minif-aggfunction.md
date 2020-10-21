---
title: 'minif () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird minif () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6932a50d59ee3df73857bfd4230faaa2e10dd2b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251014"
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