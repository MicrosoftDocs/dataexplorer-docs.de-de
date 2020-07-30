---
title: ARG_MAX () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird ARG_MAX () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1ce8bb0635743fd6692cda3b707bbb7d88e07af9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349702"
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