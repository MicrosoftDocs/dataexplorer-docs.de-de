---
title: 'Varianz () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Varianz () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4222e0672290a6d934382dd6f922aec082a19af7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338676"
---
# <a name="variance-aggregation-function"></a>Varianz () (Aggregations Funktion)

Berechnet die Varianz der *expr* in der Gruppe, wobei die Gruppe als [Beispiel](https://en.wikipedia.org/wiki/Sample_%28statistics%29)betrachtet wird. 

* Verwendete Formel:

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="Varianz Beispiel":::

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`variance(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Gibt zurück

Der Varianz Wert von *expr* in der Gruppe.
 
## <a name="examples"></a>Beispiele

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2.5|