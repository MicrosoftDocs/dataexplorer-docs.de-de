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
ms.openlocfilehash: a5f5f554373331d66a08e7166249e8e24c4fbd7c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348784"
---
# <a name="count-aggregation-function"></a>count () (Aggregations Funktion)

Gibt die Anzahl der Datensätze pro Zusammenfassungs Gruppe zurück (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.
* Verwenden Sie [die count-Aggregations](countif-aggfunction.md) Funktion, um nur Datensätze zu zählen, die von einem Prädikat `true` zurückgegeben werden

## <a name="syntax"></a>Syntax

FAS`count()`

## <a name="returns"></a>Gibt zurück

Gibt die Anzahl der Datensätze pro Zusammenfassungs Gruppe zurück (oder insgesamt, wenn die Zusammenfassung ohne Gruppierung erfolgt).

## <a name="example"></a>Beispiel

Zählen von Ereignissen in Zuständen, beginnend mit dem Buchstaben `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|Zustand|Anzahl|
|---|---|
|WEST VIRGINIA|757|
|Wyoming|396|
|Washingtons|261|
|Wisconsin|1850|
