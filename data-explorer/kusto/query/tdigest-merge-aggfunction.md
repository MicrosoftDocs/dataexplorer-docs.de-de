---
title: tdigest_merge () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird tdigest_merge () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 6f15e0028bda40a2d65349a7840861c9060ff805
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87341243"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge () (Aggregations Funktion)

Führt tdigest-Ergebnisse in der Gruppe zusammen. 

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md)Fassung verwendet werden.

Weitere Informationen zum zugrunde liegenden Algorithmus (T-Digest) und dem geschätzten Fehler [finden Sie hier](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Syntax

Zusammenfassen von `tdigest_merge(` *expr* `)` .

Zusammenfassen von `tdigest_merge(` *expr* `)` : ein Alias.

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Rückgabe

Die zusammengeführten tdigest-Werte von *expr* in der Gruppe.
 

**Tipps**

1) Sie können die-Funktion [ `percentile_tdigest()` ] (Percentile-tdigestfunction.MD) verwenden.

2) Alle tdigests, die in derselben Gruppe enthalten sind, müssen denselben Typ aufweisen.