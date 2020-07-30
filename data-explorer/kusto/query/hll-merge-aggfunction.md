---
title: hll_merge () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird hll_merge () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4681f92155181f85cad5c46ed70a79cb173d437f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347543"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (Aggregations Funktion)

`HLL`Führt Ergebnisse in der Gruppe zu einem einzelnen `HLL` Wert zusammen.

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md)Fassung verwendet werden.

Weitere Informationen finden Sie unter dem [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und der Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntax

`summarize``hll_merge(` *Expr*`)`

## <a name="arguments"></a>Argumente

* `*Expr*`: Ausdruck, der für die Aggregations Berechnung verwendet wird.

## <a name="returns"></a>Rückgabe

Die-Funktion gibt die zusammengeführten `hll` Werte von `*Expr*` in der Gruppe zurück.
 
**Tipps**

1) Verwenden Sie die Funktion [dcount_hll] (DCount-hllfunction.MD), um `dcount` aus `hll`  /  `hll-merge` Aggregations Funktionen zu berechnen.
