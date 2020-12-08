---
title: 'count() (Aggregationsfunktion): Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Aggregationsfunktion count() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513096"
---
# <a name="count-aggregation-function"></a>count() (Aggregationsfunktion)

Gibt die Anzahl der Datensätze pro Zusammenfassungsgruppe zurück (oder die Gesamtzahl, wenn die Zusammenfassung ohne Gruppierung erfolgt).

* Kann nur im Kontext der Aggregation in [summarize](summarizeoperator.md) verwendet werden.
* Verwenden Sie die [countif](countif-aggfunction.md)-Aggregationsfunktion, um nur Datensätze zu zählen, für die ein Prädikat `true` zurückgibt.

## <a name="syntax"></a>Syntax

summarize `count()`

## <a name="returns"></a>Gibt zurück

Gibt die Anzahl der Datensätze pro Zusammenfassungsgruppe zurück (oder die Gesamtzahl, wenn die Zusammenfassung ohne Gruppierung erfolgt).

## <a name="example"></a>Beispiel

Zählen von Ereignissen in Bundesstaaten, die mit dem Buchstaben `W` beginnen:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|State|Anzahl|
|---|---|
|WEST VIRGINIA|757|
|WYOMING|396|
|WASHINGTON|261|
|WISCONSIN|1850|
