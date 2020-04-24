---
title: arg_max() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird arg_max() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 73953a17b1819081c8458d5dbde3fa6d55c8866e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518954"
---
# <a name="arg_max-aggregation-function"></a>arg_max() (Aggregationsfunktion)

Sucht eine Zeile in der Gruppe, die *ExprToMaximize*maximiert und `*` den Wert von *ExprToReturn* zurückgibt (oder die gesamte Zeile zurückgibt).

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`summarize`[`(`*NameExprToMaximieren von* `,` *NameExprToReturn* [`,` ...] `)=` `arg_max` ] `(` *ExprToMaximieren*, `*`  |  *ExprToReturn* [`,` ...]`)`

**Argumente**

* *ExprToMaximize*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 
* *ExprToReturn*: Ausdruck, der zum Zurückgeben des Werts verwendet wird, wenn *ExprToMaximize* maximal ist. Der zurückzugebende Ausdruck kann ein Platzhalter (*) sein, um alle Spalten der Eingabetabelle zurückzugeben.
* *NameExprToMaximize*: Ein optionaler Name für die Ergebnisspalte, die *ExprToMaximize*darstellt.
* *NameExprToReturn*: Zusätzliche optionale Namen für die Ergebnisspalten, die *ExprToReturn*darstellen.

**Rückgabe**

Sucht eine Zeile in der Gruppe, die *ExprToMaximize*maximiert und `*` den Wert von *ExprToReturn* zurückgibt (oder die gesamte Zeile zurückgibt).

**Beispiele**

Beispiele für [arg_min()](arg-min-aggfunction.md) Aggregationsfunktion