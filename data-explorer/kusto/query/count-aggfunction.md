---
title: count () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird count () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.openlocfilehash: 6a06be43773a356e903b25b2697e75b8342ed7f8
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133471"
---
# <a name="count-aggregation-function"></a>count () (Aggregations Funktion)

Gibt die Anzahl der Datensätze pro Zusammenfassungs Gruppe zurück (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.
* Verwenden Sie [die count-Aggregations](countif-aggfunction.md) Funktion, um nur Datensätze zu zählen, die von einem Prädikat `true` zurückgegeben werden

**Syntax**

FAS`count()`

**Rückgabe**

Gibt die Anzahl der Datensätze pro Zusammenfassungs Gruppe zurück (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

**Beispiel**

Zählen von Ereignissen in Zuständen, beginnend mit dem Buchstaben `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|State|Anzahl|
|---|---|
|WEST VIRGINIA|757|
|Wyoming|396|
|Washingtons|261|
|Wisconsin|1850|
