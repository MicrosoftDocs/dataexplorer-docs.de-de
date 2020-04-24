---
title: hll_merge() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird hll_merge() (Aggregationsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4700d5c87bf0f29f7bba86d56114a6a61092da94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514109"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge() (Aggregationsfunktion)

Führt HLL-Ergebnisse in der Gruppe in einen einzelnen HLL-Wert zusammen.

* Kann nur im Kontext der Aggregation innerhalb [von summarize](summarizeoperator.md)verwendet werden.

Lesen Sie mehr über den [zugrunde liegenden Algorithmus (*H*yper*L*og*L*og) und die Schätzgenauigkeit](dcount-aggfunction.md#estimation-accuracy).

**Syntax**

`summarize``hll_merge(` *Expr*`)`

**Argumente**

* *Expr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Die zusammengeführten hll-Werte von *Expr* in der gesamten Gruppe.
 
**Tipps**

1) Sie können die Funktion [dcount_hll] (dcount-hllfunction.md) verwenden, die die dcount aus hll / hll-merge Aggregationsfunktionen berechnet.