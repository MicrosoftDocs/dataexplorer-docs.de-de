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
ms.openlocfilehash: 22f1b36b851c6e629abd2524feb4c40c74bbb1fa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348138"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

Gibt einen eindeutigen Bezeichner zurück, der den datenshard ("Block") angibt, in dem sich der aktuelle Datensatz befindet.

Wenn diese Funktion auf berechnete Daten angewendet wird, die nicht an einen datenshard angefügt sind, wird eine leere Guid (alle Nullen) zurückgegeben.

## <a name="syntax"></a>Syntax

`extent_id()`

## <a name="returns"></a>Rückgabe

Ein Wert vom Typ `guid` , der den datenshard des aktuellen Datensatzes angibt, oder eine leere Guid (alle Nullen).

## <a name="example"></a>Beispiel

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
