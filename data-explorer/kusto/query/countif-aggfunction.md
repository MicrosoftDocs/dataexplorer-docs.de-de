---
title: countif() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt countif() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03b6f1cb959706463a73d8aa18a11144e2123492
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516982"
---
# <a name="countif-aggregation-function"></a>countif() (Aggregationsfunktion)

Gibt die Anzahl von Zeilen zurück, für die *Predicate* als `true` ausgewertet wird.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

Siehe auch - [count()-Funktion,](count-aggfunction.md) die Zeilen ohne Prädikatsausdruck zählt.

**Syntax**

`countif(` *Prädikat* zusammenfassen`)`

**Argumente**

* *Prädikat*: Ausdruck, der für die Aggregationsberechnung verwendet wird. 

**Rückgabe**

Gibt die Anzahl von Zeilen zurück, für die *Predicate* als `true` ausgewertet wird.

> [!TIP]
> Verwenden von `summarize countif(filter)` anstelle von `where filter | summarize count()`