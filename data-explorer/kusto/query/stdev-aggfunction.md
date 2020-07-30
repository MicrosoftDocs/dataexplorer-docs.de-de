---
title: 'StDev () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird StDev () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 18722a544ea3bbd0e19922d1d8988a3604b4d200
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342902"
---
# <a name="stdev-aggregation-function"></a>StDev () (Aggregations Funktion)

Berechnet die Standardabweichung von *expr* in der Gruppe, wobei die Gruppe als [Beispiel](https://en.wikipedia.org/wiki/Sample_%28statistics%29)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="StDev-Beispiel":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`stdev(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Rückgabe

Der Standardwert für die Abweichung von *expr* in der Gruppe.
 
## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1, 2, 3, 4, 5]|1.58113883008419|