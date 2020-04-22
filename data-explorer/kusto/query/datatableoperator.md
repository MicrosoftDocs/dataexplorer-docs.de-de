---
title: Datentabellenoperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Datatable-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 182f8030e3263ee5bf6bee4ca7444b0d5596e7d6
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765396"
---
# <a name="datatable-operator"></a>datatable-Operator

Gibt eine Tabelle zurück, deren Schema und Werte in der Abfrage selbst definiert sind.

Beachten Sie, dass dieser Operator über keine Pipelineeingabe verfügt.

**Syntax**

`datatable``(` *ColumnName* `:` *ColumnType* [`,` .] `)` *ScalarValue* `,` *ScalarValue* ScalarValue [ ScalarValue ...] `[``]`

**Argumente**

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: Diese definieren das Schema der Tabelle. Die verwendete Syntax entspricht genau der Syntax, die beim Definieren einer Tabelle verwendet wird (siehe [.create-Tabelle](../management/create-table-command.md)).
* *ScalarValue*: Ein konstanter Skalarwert, der in die Tabelle eingefügt werden soll. Die Anzahl der Werte muss ein ganzzahliges Vielfaches der Spalten in der Tabelle sein, *n* % und der n'th-Wert muss einen Typ haben, der der Spalte n*NumColumns*entspricht. *n*

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: Diese definieren das Schema der Tabelle.
* *ScalarValue*: Ein konstanter Skalarwert, der in die Tabelle eingefügt werden soll. Die Anzahl der Werte muss ein ganzzahliges Vielfaches der Spalten in der Tabelle sein, *n* % und der n'th-Wert muss einen Typ haben, der der Spalte n*NumColumns*entspricht. *n*

::: zone-end

**Rückgabe**

Dieser Operator gibt eine Datentabelle des angegebenen Schemas und der angegebenen Daten zurück.

**Beispiel**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
