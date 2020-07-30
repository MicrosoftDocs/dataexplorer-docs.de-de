---
title: 'Cluster () (Scope-Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt Cluster () (Scope-Funktion) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3e1f74d6605b4916a2718a00fd252141060d748f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348886"
---
# <a name="cluster-scope-function"></a>Cluster () (Scope-Funktion)

::: zone pivot="azuredataexplorer"

Ändert den Verweis der Abfrage auf einen Remote Cluster. 

```kusto
cluster('help').database('Sample').SomeTable
```

## <a name="syntax"></a>Syntax

`cluster(`*StringConstant*`)`

## <a name="arguments"></a>Argumente

* *StringConstant*: Name des Clusters, auf den verwiesen wird. Der Cluster Name kann entweder ein voll qualifizierter DNS-Name oder eine Zeichenfolge sein, die mit versehen wird `.kusto.windows.net` . Das Argument muss vor der Ausführung der Abfrage _konstant_ sein, d. h., es kann nicht aus der Auswertung der Unterabfrage stammen.

**Notizen**

* Für den Zugriff auf die Datenbank innerhalb derselben Cluster-use [Database ()](databasefunction.md) -Funktion.
* Weitere Informationen zu datenbankübergreifenden und datenbankübergreifenden Abfragen finden Sie [hier](cross-cluster-or-database-queries.md) .  

## <a name="examples"></a>Beispiele

### <a name="use-cluster-to-access-remote-cluster"></a>Verwenden von Cluster () für den Zugriff auf Remote Cluster 

Die nächste Abfrage kann auf einem beliebigen Kusto-Cluster ausgeführt werden.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Anzahl|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Verwenden von Cluster () in Let-Anweisungen 

Die gleiche Abfrage wie oben kann so umgeschrieben werden, dass Sie eine Inline Funktion (Let-Anweisung) verwendet, die einen Parameter empfängt, `clusterName` der an die Cluster ()-Funktion übergeben wird.

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|Anzahl|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>Verwenden von Cluster () innerhalb von Funktionen 

Dieselbe Abfrage wie oben kann so umgeschrieben werden, dass Sie in einer Funktion verwendet wird, die einen Parameter empfängt, der an `clusterName` die Cluster ()-Funktion übergeben wird.

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**Hinweis:** solche Funktionen können nur lokal und nicht in der Cluster übergreifenden Abfrage verwendet werden.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
