---
title: 'databel-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Datenträger Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2a5881eacd0702720b7ea4b9a3237731a56a5180
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265038"
---
# <a name="datatable-operator"></a>datatable-Operator

Gibt eine Tabelle zurück, deren Schema und Werte in der Abfrage selbst definiert sind.

> [!NOTE]
> Dieser Operator hat keine Pipeline Eingabe.

**Syntax**

`datatable``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *scalarvalue* [ `,` *scalarvalue* ...]`]`

**Argumente**

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: diese Argumente definieren das Schema der Tabelle. Die Argumente verwenden dieselbe Syntax, die beim Definieren einer Tabelle verwendet wird.
  Weitere Informationen finden Sie unter [. Create Table](../management/create-table-command.md)).
* *Scalarvalue*: ein konstanter Skalarwert, der in die Tabelle eingefügt werden soll. Die Anzahl der Werte muss ein ganzzahliges Vielfaches der Spalten in der Tabelle sein. Der *n*' th-Wert muss einen Typ aufweisen, der der Spalte *n*'  %  *NumColumns*' entspricht.

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: diese Argumente definieren das Schema der Tabelle.
* *Scalarvalue*: ein konstanter Skalarwert, der in die Tabelle eingefügt werden soll. Die Anzahl der Werte muss ein ganzzahliges Vielfaches der Spalten in der Tabelle sein. Der *n*' th-Wert muss einen Typ aufweisen, der der Spalte *n*'  %  *NumColumns*' entspricht.

::: zone-end

**Rückgabe**

Dieser Operator gibt eine Datentabelle des angegebenen Schemas und der Daten zurück.

**Beispiel**

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
