---
title: maxif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt maxif () (Aggregations Funktion) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4df30f50d82e0ad5af87acaaa88b55f151a2a77a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251701"
---
# <a name="maxif-aggregation-function"></a>maxif () (Aggregations Funktion)

Gibt den maximalen Wert für die Gruppe zurück, für den das *Prädikat* ausgewertet wird `true` .

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

Siehe auch- [Max ()](max-aggfunction.md) -Funktion, die den maximalen Wert über die Gruppe ohne Prädikat Ausdruck zurückgibt.

## <a name="syntax"></a>Syntax

`summarize``maxif(` *Expr*- `,` *Prädikat*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 
* *Prädikat*: Prädikat: Wenn true, wird der berechnete *expr* -Wert auf Maximum geprüft.

## <a name="returns"></a>Rückgabe

Der maximale Wert von *expr* in der Gruppe, für die das *Prädikat* ausgewertet wird `true` .

## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|