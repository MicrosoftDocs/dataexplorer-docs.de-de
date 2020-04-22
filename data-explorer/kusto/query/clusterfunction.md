---
title: cluster() (Bereichsfunktion) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird cluster() (Bereichsfunktion) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c5f537b47006af4035c9db26388c1d4110c69b55
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766106"
---
# <a name="cluster-scope-function"></a>cluster() (Bereichsfunktion)

::: zone pivot="azuredataexplorer"

Ändert den Verweis der Abfrage in einen Remotecluster. 

```kusto
cluster('help').database('Sample').SomeTable
```

**Syntax**

`cluster(`*stringConstant*`)`

**Argumente**

* *stringConstant*: Name des Clusters, auf den verwiesen wird. Der Clustername kann entweder ein vollqualifizierter DNS-Name oder `.kusto.windows.net`eine Zeichenfolge sein, die mit suffix wird. Das Argument muss vor der Ausführung der Abfrage _konstant_ sein, d. h. es kann nicht aus der Auswertung von Unterabfragen stammen.

**Hinweise**

* Für den Zugriff auf die Datenbank innerhalb desselben Clusters - verwenden Sie die [Database()-Funktion.](databasefunction.md)
* Weitere Informationen zu clusterübergreifenden und datenbankübergreifenden Abfragen finden Sie [hier](cross-cluster-or-database-queries.md)  

## <a name="examples"></a>Beispiele

### <a name="use-cluster-to-access-remote-cluster"></a>Verwenden von Cluster() für den Zugriff auf Remotecluster 

Die nächste Abfrage kann auf jedem der Kusto-Cluster ausgeführt werden.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Anzahl|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Verwenden von cluster() inside let-Anweisungen 

Die gleiche Abfrage wie oben kann neu geschrieben werden, um die `clusterName` Inline-Funktion (let-Anweisung) zu verwenden, die einen Parameter empfängt , der an die Cluster()-Funktion übergeben wird.

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

### <a name="use-cluster-inside-functions"></a>Cluster() innerhalb von Funktionen verwenden 

Die gleiche Abfrage wie oben kann umgeschrieben werden, um `clusterName` in einer Funktion verwendet zu werden, die einen Parameter empfängt - der an die Cluster()-Funktion übergeben wird.

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**Hinweis:** Solche Funktionen können nur lokal und nicht in der clusterübergreifenden Abfrage verwendet werden.

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
