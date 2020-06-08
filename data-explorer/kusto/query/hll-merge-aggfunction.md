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
ms.openlocfilehash: 59c6f6a11b108cf6e74ceb59d3483ea1a95f7002
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512383"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (Aggregations Funktion)

`HLL`Führt Ergebnisse in der Gruppe zu einem einzelnen `HLL` Wert zusammen.

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md)Fassung verwendet werden.

Weitere Informationen finden Sie unter dem [zugrunde liegenden Algorithmus (*H*yper*L*og*l*OG) und der Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`summarize``hll_merge(` *Expr*`)`

**Argumente**

* `*Expr*`: Ausdruck, der für die Aggregations Berechnung verwendet wird.

**Rückgabe**

Die-Funktion gibt die zusammengeführten `hll` Werte von `*Expr*` in der Gruppe zurück.
 
**Tipps**

1) Verwenden Sie die Funktion [dcount_hll] (DCount-hllfunction.MD), um `dcount` aus `hll`  /  `hll-merge` Aggregations Funktionen zu berechnen.
