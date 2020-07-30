---
title: 'min () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird "min ()" (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: c7b3b189a85f46cb577c37a956c35bc755321d68
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346778"
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