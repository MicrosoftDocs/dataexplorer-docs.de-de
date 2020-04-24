---
title: min() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt min() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: ca50210c84b39f19e6717b27089313d0d116e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512409"
---
# <a name="min-aggregation-function"></a>min() (Aggregationsfunktion)

Gibt den Mindestwert in der gruppe zurück. 

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``min(` *Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Der Mindestwert von *Expr* in der gesamten Gruppe.
 
> [!TIP]
> Dies gibt Ihnen die min oder max auf eigene Faust - zum Beispiel, der höchste oder niedrigste Preis. Wenn Sie jedoch andere Spalten in der Zeile wünschen - z. B. den Namen des Lieferanten mit dem niedrigsten Preis - verwenden Sie [arg_max](arg-max-aggfunction.md) oder [arg_min](arg-min-aggfunction.md).