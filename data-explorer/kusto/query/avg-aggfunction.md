---
title: avg() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt avg() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: aadc756bdf4c6cab805f58a8a600815cf29680f7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518342"
---
# <a name="avg-aggregation-function"></a>avg() (Aggregationsfunktion)

Berechnet den Durchschnitt von *Expr* in der gesamten Gruppe. 

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`avg(` *Expr* zusammenfassen`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. Datensätze `null` mit Werten werden ignoriert und nicht in die Berechnung einbezogen.

**Rückgabe**

Der durchschnittliche Wert von *Expr* in der gesamten Gruppe.
 