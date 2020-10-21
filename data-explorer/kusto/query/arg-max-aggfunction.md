---
title: ARG_MAX () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird ARG_MAX () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: bd3d0b2c9d0c68ae646960b0b4b0b3ca41c0bb43
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248146"
---
# <a name="arg_max-aggregation-function"></a>ARG_MAX () (Aggregations Funktion)

Sucht eine Zeile in der Gruppe, die *exprtomaximize*maximiert, und gibt den Wert von *exprtoreturn* zurück (oder `*` , um die gesamte Zeile zurückzugeben).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize`[ `(` *Nameexprtomaximize* `,` *nameexprtoreturn* [ `,` ...] `)=` ] `arg_max` `(` *Exprtomaximize*, `*`  |  *exprtoreturn* [ `,` ...]`)`

## <a name="arguments"></a>Argumente

* *Exprtomaximize*: der Ausdruck, der für die Aggregations Berechnung verwendet wird. 
* *Exprtoreturn*: Ausdruck, der zum Zurückgeben des Werts verwendet wird, wenn *exprtomaximize* den maximalen Wert hat. Der zurück zugebende Ausdruck kann ein Platzhalter Zeichen (*) sein, um alle Spalten der Eingabe Tabelle zurückzugeben.
* *Nameexprtomaximize*: ein optionaler Name für die Ergebnisspalte, die *exprtomaximize*darstellt.
* *Nameexprtoreturn*: zusätzliche optionale Namen für die Ergebnis Spalten, die " *exprtoreturn*" darstellen.

## <a name="returns"></a>Rückgabe

Sucht eine Zeile in der Gruppe, die *exprtomaximize*maximiert, und gibt den Wert von *exprtoreturn* zurück (oder `*` , um die gesamte Zeile zurückzugeben).

## <a name="examples"></a>Beispiele

Siehe Beispiele für [arg_min ()](arg-min-aggfunction.md) -Aggregations Funktion