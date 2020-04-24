---
title: max() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt max() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: bbfc9591fb20903d18486f9f249d3b1240f705e3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512562"
---
# <a name="max-aggregation-function"></a>max() (Aggregationsfunktion)

Gibt den Maximalwert in der gruppe zurück. 

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize``max(` *Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Der maximale Wert von *Expr* in der gesamten Gruppe.
 
> [!TIP]
> Dies gibt Ihnen die min oder max auf eigene Faust - zum Beispiel, der höchste oder niedrigste Preis.
> Wenn Sie jedoch andere Spalten in der Zeile wünschen - z. B. den Namen des Lieferanten mit dem niedrigsten Preis - verwenden Sie [arg_max](arg-max-aggfunction.md) oder [arg_min](arg-min-aggfunction.md).