---
title: maxif () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt maxif () (Aggregations Funktion) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 471ca0e3d6623b77fd2d799949bfe060643798e2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346812"
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