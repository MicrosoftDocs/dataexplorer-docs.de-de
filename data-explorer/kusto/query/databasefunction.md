---
title: 'Database () (Scope-Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Datenbank () (Scope-Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 03753cf7dcabb7637335d3dc71f0b9bca773e710
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030445"
---
# <a name="database-scope-function"></a>Database () (Scope-Funktion)

::: zone pivot="azuredataexplorer"

Ändert den Verweis der Abfrage auf eine bestimmte Datenbank innerhalb des Cluster Bereichs. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

**Syntax**

`database(`*StringConstant*`)`

**Argumente**

* *StringConstant*: Name der Datenbank, auf die verwiesen wird. Die identifizierte Datenbank kann entweder `DatabaseName` oder `PrettyName`sein. Das Argument muss vor der Ausführung der Abfrage _konstant_ sein, d. h., es kann nicht aus der Auswertung der Unterabfrage stammen.

**Hinweise**

* Informationen zum Zugreifen auf Remote Cluster und Remote Datenbanken finden Sie unter [Cluster ()](clusterfunction.md) Scope-Funktion.
* Weitere Informationen zu datenbankübergreifenden und datenbankübergreifenden Abfragen finden Sie [hier](cross-cluster-or-database-queries.md) .

## <a name="examples"></a>Beispiele

### <a name="use-database-to-access-table-of-other-database"></a>Verwenden Sie Database (), um auf die Tabelle einer anderen Datenbank zuzugreifen. 

```kusto
database('Samples').StormEvents | count
```

|Anzahl|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>Verwenden von Database () in Let-Anweisungen 

Dieselbe Abfrage wie oben kann so umgeschrieben werden, dass Sie eine Inline Funktion (Let-Anweisung) `dbName` verwendet, die einen Parameter empfängt, der an die Database ()-Funktion übergeben wird.

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|Anzahl|
|---|
|59066|

### <a name="use-database-inside-functions"></a>Verwenden von Database () innerhalb von Funktionen 

Dieselbe Abfrage wie oben kann so umgeschrieben werden, dass Sie in einer Funktion verwendet wird, `dbName` die einen Parameter empfängt, der an die Database ()-Funktion übergeben wird.

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**Hinweis:** solche Funktionen können nur lokal und nicht in der Cluster übergreifenden Abfrage verwendet werden.

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
