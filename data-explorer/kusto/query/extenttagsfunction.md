---
title: extent_tags() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird extent_tags() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d8af7e51c5e2efb16763541db05e9ccc7e2cb95f
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765433"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

Gibt ein dynamisches Array mit den [Tags](../management/extents-overview.md#extent-tagging) des Daten-Shards ("Extent") zurück, in dem sich der aktuelle Datensatz befindet. 

Wenn Sie diese Funktion auf berechnete Daten anwenden, die nicht an einen Datenshard angefügt sind, wird ein leerer Wert zurückgegeben.

**Syntax**

`extent_tags()`

**Rückgabe**

Ein Wert `dynamic` des Typs, der ein Array mit den Ausdehnungstags des aktuellen Datensatzes oder ein leerer Wert ist.

**Beispiele**

Das folgende Beispiel zeigt, wie Sie eine Liste der Tags aller Datenshards abrufen, die `ActivityId`Datensätze von vor einer Stunde mit einem bestimmten Wert für die Spalte haben. Es zeigt, dass einige Abfrageoperatoren (hier der `where` `extend` Operator, aber dies gilt auch für und `project`) die Informationen über den Daten-Shard, der den Datensatz hostet, beibehalten.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

Das folgende Beispiel zeigt, wie Sie eine Anzahl aller Datensätze aus der letzten Stunde abrufen, die in Ausdehnungen gespeichert werden, die mit dem Tag `MyTag` (und möglicherweise anderen Tags) markiert, aber nicht mit dem Tag `drop-by:MyOtherTag`markiert sind.

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
