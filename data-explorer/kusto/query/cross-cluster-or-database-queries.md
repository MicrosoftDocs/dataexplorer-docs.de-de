---
title: 'Datenbankübergreifende und Cluster übergreifende Abfragen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt datenbankübergreifende und Cluster übergreifende Abfragen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 834fd81e1832b8ab624da8d99cb5cc32407db84f
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741760"
---
# <a name="cross-database-and-cross-cluster-queries"></a>Datenbankübergreifende und clusterübergreifende Abfragen

::: zone pivot="azuredataexplorer"

Jede Kusto-Abfrage wird im Kontext des aktuellen Clusters und der Standarddatenbank durchführt.
* Im [Kusto-Explorer](../tools/kusto-explorer.md) ist die Standarddatenbank die im [Verbindungs Panel](../tools/kusto-explorer.md#connections-panel) ausgewählte Datenbank, und der aktuelle Cluster ist die Verbindung, die diese Datenbank enthält.
* Wenn die [Kusto-Client Bibliothek](../api/netfx/about-kusto-data.md) verwendet wird, werden der aktuelle Cluster und die Standard `Data Source` Datenbank `Initial Catalog` durch die-Eigenschaft und die-Eigenschaft der [Kusto-Verbindungs](../api/connection-strings/kusto.md) Zeichenfolgen angegeben.

## <a name="queries"></a>Abfragen
Für den Zugriff auf Tabellen aus einer anderen Datenbank als der Standardeinstellung muss die *qualifizierte namens* Syntax verwendet werden: für den Zugriff auf die Datenbank im aktuellen Cluster:
```kusto
database("<database name>").<table name>
```
Datenbank im Remote Cluster:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

beim *Datenbanknamen* wird Groß-/Kleinschreibung

beim *Cluster Namen* wird die Groß-/Kleinschreibung nicht beachtet, und Sie können eine der folgenden Formen aufweisen:
* Wohlgeformte URL: Beispiel `http://contoso.kusto.windows.net:1234/`: nur http-und HTTPS-Schemas werden unterstützt.
* Vollständig qualifizierter Domänen Name (Fully Qualified Domain Name, `contoso.kusto.windows.net` FQDN): beispielsweise.`https://`**`contoso.kusto.windows.net`**`:443/`
* Kurzname (hostname [und Region] ohne den `contoso` Domänen Teil): beispielsweise, der als `https://` **`contoso`** `.kusto.windows.net:443/`oder `contoso.westus` interpretiert wird, das als`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> Der datenbankübergreifende Zugriff unterliegt den üblichen Berechtigungs Überprüfungen.
> Zum Erweitern einer Abfrage müssen Sie über die Berechtigung Lesen für die Standarddatenbank und jede andere Datenbank verfügen, auf die in der Abfrage verwiesen wird (in den aktuellen und Remote Clustern).

Der *qualifizierte Name* kann in jedem Kontext verwendet werden, in dem ein Tabellenname verwendet werden kann.
Folgendes ist gültig:

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

Wenn *qualifizierter Name* als Operand des [Union-Operators](./unionoperator.md)angezeigt wird, können Platzhalter verwendet werden, um mehrere Tabellen und mehrere Datenbanken anzugeben. Platzhalter sind in Cluster Namen nicht zulässig:

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* Der Name der Standarddatenbank ist auch eine mögliche Entsprechung, daher gibt Database ("&#42;"). * alle Tabellen aller Datenbanken einschließlich der Standardeinstellung an.
>* Zur Auswirkung von Schema Änderungen auf Cluster übergreifende Abfragen finden Sie unter [Cluster übergreifende Abfragen und Schema Änderungen](../concepts/crossclusterandschemachanges.md) .

## <a name="access-restriction"></a>Zugriffs Einschränkung 
Qualifizierte Namen oder Muster können auch in der [Einschränkung Access](./restrictstatement.md) -Anweisung enthalten sein (Platzhalter in Cluster Namen sind nicht zulässig).
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

Im obigen Beispiel wird der Abfrage Zugriff auf die folgenden Entitäten eingeschränkt:

* Alle Entitäts Namen, die mit " *My...* " in der Standarddatenbank beginnen. 
* Eine beliebige Tabelle in allen Datenbanken mit dem Namen *MyOther...* des aktuellen Clusters.
* Eine beliebige Tabelle in allen Datenbanken mit dem Namen *MY2...* im Cluster *OtherCluster.Kusto.Windows.net*.

## <a name="functions-and-views"></a>Funktionen und Ansichten

Funktionen und Sichten (persistent und erstellt Inline) können Tabellen über Daten Bank-und Cluster Grenzen hinweg referenzieren. Folgendes ist gültig:

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

Auf persistente Funktionen und Sichten kann von einer anderen Datenbank im gleichen Cluster zugegriffen werden:

Tabellarische Funktion (Sicht) `OtherDb`in:

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

Skalarfunktion in `OtherDb`:
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

In der Standarddatenbank:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>Einschränkungen von Cluster übergreifenden Funktionsaufrufen

Auf tabellarische Funktionen oder Sichten kann Cluster übergreifend verwiesen werden. Die folgenden Einschränkungen gelten:

1. Die Remote Funktion muss ein tabellarisches Schema zurückgeben. Auf skalare Funktionen kann nur im gleichen Cluster zugegriffen werden.
2. Die Remote Funktion kann nur skalare Parameter akzeptieren. Auf Funktionen, die mindestens ein Tabellen Argument abrufen, kann nur im gleichen Cluster zugegriffen werden.
3. Das Schema der Remote Funktion muss bekannt und unveränderlich sein (siehe auch [Cluster übergreifende Abfragen und Schema Änderungen](../concepts/crossclusterandschemachanges.md)).

Der folgende Cluster übergreifende aufrufswert ist **gültig**:

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

Die folgende Abfrage ruft die Remote-Skalarfunktion `MyCalc`auf.
Dies verstößt gegen Regel #1 und ist daher **ungültig**:

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

Die folgende Abfrage ruft die Remote `MyCalc` Funktion auf und stellt einen tabellarischen Parameter bereit.
Dies verstößt gegen Regel #2 und ist daher **ungültig**:

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

Die folgende Abfrage ruft eine Remote `SomeTable` Funktion auf, die über eine Variable Schema Ausgabe `tablename`auf Grundlage des-Parameters verfügt.
Dies verstößt gegen Regel #3 und ist daher **ungültig**:

Tabellarische Funktion `OtherDb`in:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

In der Standarddatenbank:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

Die folgende Abfrage ruft eine Remote `GetDataPivot` Funktion auf, die auf der Grundlage des Data ([Pivot ()-Plug](pivotplugin.md) -ins auf der Grundlage der dynamischen Ausgabe eine Variable Schema Ausgabe aufweist
Dies verstößt gegen Regel #3 und ist daher **ungültig**:

Tabellarische Funktion `OtherDb`in:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

Tabellarische Funktion in der Standarddatenbank:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>Anzeigen von Daten

Anweisungen, die Daten an den Client zurückgeben, werden implizit durch die Anzahl der zurückgegebenen Datensätze begrenzt, auch wenn es keine `take` bestimmte Verwendung des Operators gibt. Um diese Begrenzung aufzuheben, verwenden Sie die Clientanforderungsoption `notruncation` .

Verwenden Sie den [Rendering-Operator](renderoperator.md), um Daten in grafischer Form anzuzeigen.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
