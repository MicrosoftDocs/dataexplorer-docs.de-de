---
title: 'Datenbankübergreifende &-Cluster übergreifende Abfragen: Azure Daten-Explorer'
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
ms.openlocfilehash: 26c6d660cb254ec2df6600e90437d7db7ca748f4
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863148"
---
# <a name="cross-database-and-cross-cluster-queries"></a>Datenbankübergreifende und clusterübergreifende Abfragen

::: zone pivot="azuredataexplorer"

Jede Kusto-Abfrage wird im Kontext des aktuellen Clusters und der Standarddatenbank durchführt.
* Im [Kusto-Explorer](../tools/kusto-explorer.md) ist die Standarddatenbank die im [Verbindungs Panel](../tools/kusto-explorer.md#connections-panel) ausgewählte Datenbank, und der aktuelle Cluster ist die Verbindung, die diese Datenbank enthält.
* Wenn die [Kusto-Client Bibliothek](../api/netfx/about-kusto-data.md) verwendet wird, werden der aktuelle Cluster und die Standarddatenbank durch die `Data Source` -Eigenschaft und die-Eigenschaft `Initial Catalog` der [Kusto-Verbindungs](../api/connection-strings/kusto.md) Zeichenfolgen angegeben.

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
* Wohlgeformte URL, z. b `http://contoso.kusto.windows.net:1234/` .. Nur http-und HTTPS-Schemas werden unterstützt.
* Vollständig qualifizierter Domänen Name (FQDN), z `contoso.kusto.windows.net` . b.. Diese Zeichenfolge entspricht `https://` **`contoso.kusto.windows.net`** `:443/` .
* Kurzname (hostname [und Region] ohne Domänen Teil), z `contoso` . b `contoso.westus` . oder. Diese Zeichen folgen werden als `https://` **`contoso`** `.kusto.windows.net:443/` und interpretiert `https://` **`contoso.westus`** `.kusto.windows.net:443/` .

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

Funktionen und Sichten (persistent und erstellt Inline) können über Daten Bank-und Cluster Grenzen hinweg auf Tabellen verweisen. Der folgende Code ist gültig:

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

Auf persistente Funktionen und Sichten kann von einer anderen Datenbank im gleichen Cluster zugegriffen werden:

Tabellarische Funktion (Sicht) in `OtherDb` :

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

Skalarfunktion in `OtherDb` :
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

In der Standarddatenbank:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>Einschränkungen von Cluster übergreifenden Funktionsaufrufen

Auf tabellarische Funktionen oder Sichten kann Cluster übergreifend verwiesen werden. Es gelten die folgenden Einschränkungen:

1. Die Remote Funktion muss ein tabellarisches Schema zurückgeben. Auf skalare Funktionen kann nur im gleichen Cluster zugegriffen werden.
2. Die Remote Funktion kann nur skalare Parameter akzeptieren. Auf Funktionen, die mindestens ein Tabellen Argument abrufen, kann nur im gleichen Cluster zugegriffen werden.
3. Das Schema der Remote Funktion muss bekannt und unveränderlich sein (siehe auch [Cluster übergreifende Abfragen und Schema Änderungen](../concepts/crossclusterandschemachanges.md)).

Der folgende Cluster übergreifende aufrufswert ist **gültig**:

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

Die folgende Abfrage ruft die Remote-Skalarfunktion auf `MyCalc` .
Dieser Rückruf verstößt gegen die Regel #1, daher ist er **ungültig**:

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

Die folgende Abfrage ruft die Remote Funktion auf `MyCalc` und stellt einen tabellarischen Parameter bereit.
Dieser Rückruf verstößt gegen die Regel #2, daher ist er **ungültig**:

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

Die folgende Abfrage ruft eine Remote Funktion `SomeTable` auf, die über eine Variable Schema Ausgabe auf Grundlage des-Parameters verfügt `tablename` .
Dieser Rückruf verstößt gegen die Regel #3, daher ist er **ungültig**:

Tabellarische Funktion in `OtherDb` :
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

In der Standarddatenbank:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

Die folgende Abfrage ruft eine Remote Funktion `GetDataPivot` auf, die auf der Grundlage des Data ([Pivot ()-Plug](pivotplugin.md) -ins auf der Grundlage der dynamischen Ausgabe eine Variable Schema Ausgabe aufweist
Dieser Rückruf verstößt gegen die Regel #3, daher ist er **ungültig**:

Tabellarische Funktion in `OtherDb` :
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

Tabellarische Funktion in der Standarddatenbank:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>Anzeigen von Daten

Anweisungen, die Daten an den Client zurückgeben, werden implizit durch die Anzahl der zurückgegebenen Datensätze begrenzt, auch wenn es keine bestimmte Verwendung des `take` Operators gibt. Um diese Begrenzung aufzuheben, verwenden Sie die Clientanforderungsoption `notruncation` .

Verwenden Sie den [Rendering-Operator](renderoperator.md), um Daten in grafischer Form anzuzeigen.

::: zone-end

::: zone pivot="azuremonitor"

Datenbankübergreifende und Cluster übergreifende Abfragen werden in Azure Monitor nicht unterstützt.

::: zone-end
