---
title: STDE VP () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird STDE VP () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16ae0e297dacefb3a9cc8bc7efb579393d89c968
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243830"
---
# <a name="stdevp-aggregation-function"></a>STDE VP () (Aggregations Funktion)

Berechnet die Standardabweichung von *expr* in der Gruppe, wobei die Gruppe als [Population](https://en.wikipedia.org/wiki/Statistical_population)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="StDev-Population":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`stdevp(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Rückgabe

Der Standardwert für die Abweichung von *expr* in der Gruppe.
 
## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1, 2, 3, 4, 5]|1.4142135623731|