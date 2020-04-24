---
title: tdigest_merge() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird tdigest_merge() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0b7de916dd53c19a49301c8048e2d8867d1b1249
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506391"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge() (Aggregationsfunktion)

Führt tdigest-Ergebnisse in der gruppe. 

* Kann nur im Kontext der Aggregation innerhalb [von summarize](summarizeoperator.md)verwendet werden.

Lesen Sie [hier](percentiles-aggfunction.md#estimation-error-in-percentiles)mehr über den zugrunde liegenden Algorithmus (T-Digest) und den geschätzten Fehler .

**Syntax**

`tdigest_merge(` *Expr*`)`zusammenfassen .

`tdigest_merge(` *Expr* `)` - Ein Alias zusammenfassen.

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Die zusammengeführten tdigest-Werte von *Expr* in der gesamten Gruppe.
 

**Tipps**

1) Sie können die`percentile_tdigest()`Funktion [ ] (percentile-tdigestfunction.md) verwenden.

2) Alle tdigests, die in derselben Gruppe enthalten sind, müssen vom gleichen Typ sein.