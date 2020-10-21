---
title: hll_merge () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird hll_merge () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a6aa19e3a88e7338494d6f6fad0e23a5ab4a03e4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252304"
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
