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
ms.openlocfilehash: 1bfe42e18cfe0bb424e933b266eb9861c7676cea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348580"
---
# <a name="database-scope-function"></a>Database () (Scope-Funktion)

::: zone pivot="azuredataexplorer"

Ändert den Verweis der Abfrage auf eine bestimmte Datenbank innerhalb des Cluster Bereichs. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

## <a name="syntax"></a>Syntax

`database(`*StringConstant*`)`

## <a name="arguments"></a>Argumente

* *StringConstant*: Name der Datenbank, auf die verwiesen wird. Die identifizierte Datenbank kann entweder `DatabaseName` oder sein `PrettyName` . Das Argument muss vor der Ausführung der Abfrage _konstant_ sein, d. h., es kann nicht aus der Auswertung der Unterabfrage stammen.

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

Dieselbe Abfrage wie oben kann so umgeschrieben werden, dass Sie eine Inline Funktion (Let-Anweisung) verwendet, die einen Parameter empfängt, `dbName` der an die Database ()-Funktion übergeben wird.

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

Dieselbe Abfrage wie oben kann so umgeschrieben werden, dass Sie in einer Funktion verwendet wird, die einen Parameter empfängt, der an `dbName` die Database ()-Funktion übergeben wird.

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**Hinweis:** solche Funktionen können nur lokal und nicht in der Cluster übergreifenden Abfrage verwendet werden.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
