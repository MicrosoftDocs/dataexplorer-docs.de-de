---
title: extent_id ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird extent_id () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 300f7961fd11b433ef4e420d5a20b9ad9150b269
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737622"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

Gibt einen eindeutigen Bezeichner zurück, der den datenshard ("Block") angibt, in dem sich der aktuelle Datensatz befindet. 

Wenn diese Funktion auf berechnete Daten angewendet wird, die nicht an einen datenshard angefügt sind, wird eine leere Guid (alle Nullen) zurückgegeben.

**Syntax**

`extent_id()`

**Rückgabe**

Ein Wert vom Typ `guid` , der den datenshard des aktuellen Datensatzes angibt, oder eine leere Guid (alle Nullen).

**Beispiel**

Im folgenden Beispiel wird gezeigt, wie Sie eine Liste aller Daten-Shards, die über Datensätze von einer Stunde verfügen, mit einem bestimmten Wert `ActivityId`für die Spalte erhalten. Es veranschaulicht, dass einige Abfrage Operatoren (hier `where` der-Operator, aber dies gilt auch `extend` für `project`und) die Informationen über den datenshard, der den Datensatz gehostet, beibehalten.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend eid=extent_id()
| summarize by eid
```

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
