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
ms.openlocfilehash: 1f5584233a24c834e0ca6c28ed60aa5d7496b411
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291524"
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

Im folgenden Beispiel wird gezeigt, wie Sie eine Liste aller Daten-Shards, die über Datensätze von einer Stunde verfügen, mit einem bestimmten Wert für die Spalte erhalten `ActivityId` . Es veranschaulicht, dass einige Abfrage Operatoren (hier, der- `where` Operator und und `extend` `project` ) die Informationen über den datenshard, der den Datensatz gehostet, beibehalten.

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
