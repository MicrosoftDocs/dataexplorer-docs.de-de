---
title: tdigest_merge () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird tdigest_merge () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 14b6b1c5ed31c312065e7641783bd1dc524993d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250644"
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