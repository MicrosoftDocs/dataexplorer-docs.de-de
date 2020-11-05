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
ms.openlocfilehash: 59813656fce6afea3ecba62b13c971e74b095fe1
ms.sourcegitcommit: 42cc7d11f41a5bfa9e021764b044dcd68d99a258
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93403713"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (Aggregations Funktion)

`HLL`Führt Ergebnisse in der Gruppe zu einem einzelnen `HLL` Wert zusammen.

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md)Fassung verwendet werden.

Weitere Informationen finden Sie unter dem [zugrunde liegenden Algorithmus ( *H* yper *L* og *l* OG) und der Schätzung der Schätzung](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntax

`summarize``hll_merge(` *Expr*`)`

## <a name="arguments"></a>Argumente

* `*Expr*`: Ausdruck, der für die Aggregations Berechnung verwendet wird.

## <a name="returns"></a>Gibt zurück

Die-Funktion gibt die zusammengeführten `hll` Werte von `*Expr*` in der Gruppe zurück.
 
**Tipps**

1) Verwenden Sie die Funktion [dcount_hll](dcount-hllfunction.md) , um `dcount` aus `hll`  /  `hll-merge` Aggregations Funktionen zu berechnen.
