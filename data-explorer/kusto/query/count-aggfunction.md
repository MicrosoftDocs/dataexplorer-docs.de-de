---
title: count() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt count() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 59b898d44507d844db1f714ef15effa004e5546f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516999"
---
# <a name="count-aggregation-function"></a>count() (Aggregationsfunktion)

Gibt eine Anzahl der Datensätze pro Zusammenfassungsgruppe zurück (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden
* Verwenden Sie die [Aggregationsfunktion countif,](countif-aggfunction.md) um nur `true`Datensätze zu zählen, für die einige Prädikatsrückgaben .

**Syntax**

Zusammenfassen`count()`

**Rückgabe**

Gibt eine Anzahl der Datensätze pro Zusammenfassungsgruppe zurück (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).