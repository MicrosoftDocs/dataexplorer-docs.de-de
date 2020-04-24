---
title: ingestion_time ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird ingestion_time () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2474b8751be5cba2270bcbd2936c76b91f5f3ba0
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030037"
---
# <a name="ingestion_time"></a>ingestion_time()

Ruft die `$IngestionTime` verborgene `datetime` Spalte des Datensatzes oder NULL ab.
Die `$IngestionTime` Spalte wird automatisch definiert, wenn der der Tabelle

::: zone pivot="azuredataexplorer"

Die [ingestiontime-Richtlinie](../management/ingestiontimepolicy.md) ist festgelegt (aktiviert).

::: zone-end

::: zone pivot="azuremonitor"

Die ingestiontime-Richtlinie ist festgelegt (aktiviert).

::: zone-end

Wenn diese Richtlinie in der Tabelle nicht definiert ist, wird ein NULL-Wert zurückgegeben.

Diese Funktion muss im Kontext einer tatsächlichen Tabelle verwendet werden, um die relevanten Daten zurückzugeben. Beispielsweise wird NULL für alle Datensätze zurückgegeben, wenn Sie nach einem `summarize` Operator aufgerufen wird.

**Syntax**

 `ingestion_time()`

**Rückgabe**

Ein `datetime` -Wert, der die ungefähre Zeit der Erfassung in eine Tabelle angibt.

**Beispiel**

```kusto
T 
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
