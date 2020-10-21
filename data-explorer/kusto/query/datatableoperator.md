---
title: 'databel-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Datenträger Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6a5f12c947d62b7ec13ab6d9d6a4564881de08a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247849"
---
# <a name="datatable-operator"></a>datatable-Operator

Gibt eine Tabelle zurück, deren Schema und Werte in der Abfrage selbst definiert sind.

> [!NOTE]
> Dieser Operator hat keine Pipeline Eingabe.

## <a name="syntax"></a>Syntax

`datatable``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *scalarvalue* [ `,` *scalarvalue* ...]`]`

## <a name="arguments"></a>Argumente

::: zone pivot="azuredataexplorer"

* *ColumnName*, *ColumnType*: diese Argumente definieren das Schema der Tabelle. Die Argumente verwenden dieselbe Syntax, die beim Definieren einer Tabelle verwendet wird.
  Weitere Informationen finden Sie unter [. Create Table](../management/create-table-command.md)).
* *Scalarvalue*: ein konstanter Skalarwert, der in die Tabelle eingefügt werden soll. Die Anzahl der Werte muss ein ganzzahliges Vielfaches der Spalten in der Tabelle sein. Der *n*' th-Wert muss einen Typ aufweisen, der der Spalte *n*'  %  *NumColumns*' entspricht.

::: zone-end

::: zone pivot="azuremonitor"

* *ColumnName*, *ColumnType*: diese Argumente definieren das Schema der Tabelle.
* *Scalarvalue*: ein konstanter Skalarwert, der in die Tabelle eingefügt werden soll. Die Anzahl der Werte muss ein ganzzahliges Vielfaches der Spalten in der Tabelle sein. Der *n*' th-Wert muss einen Typ aufweisen, der der Spalte *n*'  %  *NumColumns*' entspricht.

::: zone-end

## <a name="returns"></a>Rückgabe

Dieser Operator gibt eine Datentabelle des angegebenen Schemas und der Daten zurück.

## <a name="example"></a>Beispiel

```kusto
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```
