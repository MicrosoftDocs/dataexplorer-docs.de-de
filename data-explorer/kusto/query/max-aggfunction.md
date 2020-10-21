---
title: Max () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Max ()-Funktion (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cd53f0701064d95ab2d088e774f49b06f8d323f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251055"
---
# <a name="max-aggregation-function"></a>Max () (Aggregations Funktion)

Gibt den maximalen Wert in der Gruppe zurück. 

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``max(` *Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Rückgabe

Der maximale Wert von *expr* in der Gruppe.
 
> [!TIP]
> Dadurch erhalten Sie die minimale oder maximale Anzahl, z. b. den höchsten oder niedrigsten Preis.
> Wenn Sie jedoch andere Spalten in der Zeile verwenden möchten, z. b. der Name des Lieferanten mit dem niedrigsten Preis [ARG_MAX](arg-max-aggfunction.md) oder [arg_min](arg-min-aggfunction.md).