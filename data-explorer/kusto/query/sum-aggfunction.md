---
title: Sum () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Sum () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3053a2c3bd423a2e1b8a2690bcdf54de9f1a1e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350824"
---
# <a name="sum-aggregation-function"></a>Sum () (Aggregations Funktion)

Berechnet die Summe von *expr* in der Gruppe. 

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`sum(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Rückgabe

Der Sum-Wert von *expr* in der Gruppe.
 