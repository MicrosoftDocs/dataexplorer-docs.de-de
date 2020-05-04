---
title: extent_tags ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird extent_tags () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 146ab59c1c0cbcb86bfae94fa26c09f5afa0f216
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737588"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

Gibt ein dynamisches Array mit den [Tags](../management/extents-overview.md#extent-tagging) des datenshards ("Block") zurück, in dem sich der aktuelle Datensatz befindet. 

Wenn diese Funktion auf berechnete Daten angewendet wird, die nicht an einen datenshard angefügt sind, wird ein leerer Wert zurückgegeben.

**Syntax**

`extent_tags()`

**Rückgabe**

Ein Wert vom Typ `dynamic` , bei dem es sich um ein Array mit den Block Tags des aktuellen Datensatzes oder um einen leeren Wert handelt.

**Beispiele**

Im folgenden Beispiel wird gezeigt, wie Sie eine Liste der Tags aller datenshards, die über Datensätze von einer Stunde verfügen, mit einem bestimmten Wert für `ActivityId`die Spalte erhalten. Es veranschaulicht, dass einige Abfrage Operatoren (hier `where` der-Operator, aber dies gilt auch `extend` für `project`und) die Informationen über den datenshard, der den Datensatz gehostet, beibehalten.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

Im folgenden Beispiel wird gezeigt, wie die Anzahl aller Datensätze aus der letzten Stunde abgerufen wird, die in Blöcken gespeichert sind, die mit dem- `MyTag` Tag (und potenziell anderen Tags) gekennzeichnet sind, aber nicht mit `drop-by:MyOtherTag`dem-Tag markiert sind.

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
