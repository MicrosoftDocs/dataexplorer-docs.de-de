---
title: 'min () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird "min ()" (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: c6cd5b219b1f89e68d34b37b21135e5ec02d6ee8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248943"
---
# <a name="min-aggregation-function"></a>min () (Aggregations Funktion)

Gibt den Mindestwert in der Gruppe zurück. 

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

## <a name="syntax"></a>Syntax

`summarize``min(` *Expr*`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Rückgabe

Der minimale Wert von *expr* in der Gruppe.
 
> [!TIP]
> Dadurch erhalten Sie die minimale oder maximale Anzahl, z. b. den höchsten oder niedrigsten Preis. Wenn Sie jedoch andere Spalten in der Zeile verwenden möchten, z. b. der Name des Lieferanten mit dem niedrigsten Preis [ARG_MAX](arg-max-aggfunction.md) oder [arg_min](arg-min-aggfunction.md).