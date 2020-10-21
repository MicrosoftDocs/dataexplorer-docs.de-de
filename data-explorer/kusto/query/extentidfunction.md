---
title: extent_id ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird extent_id () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: eb1984ba80b5d2940591428fea4b1f6c3982f9d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246628"
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
