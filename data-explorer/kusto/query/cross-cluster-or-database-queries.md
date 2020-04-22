---
title: Datenbankübergreifende und clusterübergreifende Abfragen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden datenbankübergreifende und clusterübergreifende Abfragen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8bb0cf2674bae801fb98d14d93518f5617af6807
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766064"
---
# <a name="cross-database-and-cross-cluster-queries"></a>Datenbankübergreifende und clusterübergreifende Abfragen

::: zone pivot="azuredataexplorer"

Jede Kusto-Abfrage arbeitet im Kontext des aktuellen Clusters und der Standarddatenbank.
* Im [Kusto Explorer](../tools/kusto-explorer.md) ist die Standarddatenbank diejenige, die im [Bedienfeld "Verbindungen"](../tools/kusto-explorer.md#connections-panel) ausgewählt ist, und der aktuelle Cluster ist die Verbindung, die diese Datenbank enthält.
* Bei Verwendung der [Kusto-Clientbibliothek](../api/netfx/about-kusto-data.md) werden der aktuelle `Data Source` `Initial Catalog` Cluster und die Standarddatenbank durch die und Eigenschaften der [Kusto-Verbindungszeichenfolgen](../api/connection-strings/kusto.md) angegeben.

## <a name="queries"></a>Abfragen
Um auf Tabellen aus einer anderen Datenbank als der Standarddatenbank zuzugreifen, muss die *Syntax für den qualifizierten Namen* verwendet werden: Um auf die Datenbank im aktuellen Cluster zuzugreifen:
```kusto
database("<database name>").<table name>
```
Datenbank im Remotecluster:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*Bei Datenbankname* wird die Groß-/Kleinschreibung beachtet

*Der Clustername* wird bei der Groß-/Kleinschreibung nicht berücksichtigt und kann von einem der folgenden Formen sein:
* Wohlgeformte URL: `http://contoso.kusto.windows.net:1234/`Beispiel , werden nur HTTP- und HTTPS-Schemas unterstützt.
* Vollqualifizierter Domainname (FQDN): zum Beispiel `contoso.kusto.windows.net` - was gleichkommt`https://`**`contoso.kusto.windows.net`**`:443/`
* Kurzname (Hostname [und Region] ohne Den `contoso` Domainpart): `https://` **`contoso`** `.kusto.windows.net:443/`z. `contoso.westus` B. - der als interpretiert wird, oder - der als`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> Der datenbankübergreifende Zugriff unterliegt den üblichen Berechtigungsprüfungen.
> Um eine Abfrage zu löschen, müssen Sie über Leseberechtigungen für die Standarddatenbank und jede andere Datenbank verfügen, auf die in der Abfrage verwiesen wird (im aktuellen und im Remotecluster).

*Qualifizierter Name* kann in jedem Kontext verwendet werden, in dem ein Tabellenname verwendet werden kann.
Alle folgenden Punkte sind gültig:

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

Wenn der *qualifizierte Name* als Operand des [Union-Operators](./unionoperator.md)angezeigt wird, können Platzhalter verwendet werden, um mehrere Tabellen und mehrere Datenbanken anzugeben. Platzhalter sind in Clusternamen nicht zulässig:

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* Der Name der Standarddatenbank ist ebenfalls eine potenzielle Übereinstimmung, daher gibt database("&#42;").*alle Tabellen aller Datenbanken einschließlich der Standarddatenbank an.
>* Wie wirken sich Schemaänderungen auf clusterübergreifende Abfragen aus, siehe [Clusterübergreifende Abfragen und Schemaänderungen](../concepts/crossclusterandschemachanges.md)

## <a name="access-restriction"></a>Zugriffsbeschränkung 
Qualifizierte Namen oder Muster können auch in [die Einschränkungszugriffsanweisung](./restrictstatement.md) einbezogen werden (Platzhalter in Clusternamen sind nicht zulässig)
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

Die oben genannten beschränken den Abfragezugriff auf die folgenden Berechtigungen:

* Jeder Entitätsname, der mit *my...* in der Standarddatenbank beginnt. 
* Jede Tabelle in allen Datenbanken mit dem Namen *MyOther...* des aktuellen Clusters.
* Jede Tabelle in allen Datenbanken mit dem Namen *my2...* im Cluster *OtherCluster.kusto.windows.net*.

## <a name="functions-and-views"></a>Funktionen und Ansichten

Funktionen und Ansichten (persistent und erstellt inline) können Tabellen über Datenbank- und Clustergrenzen hinweg verweisen. Folgendes ist gültig:

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

Auf persistente Funktionen und Ansichten kann von einer anderen Datenbank im selben Cluster aus zugegriffen werden:

Tabellarische Funktion (Ansicht) in: `OtherDb`

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

Skalarfunktion in: `OtherDb`
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

In der Standarddatenbank:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>Einschränkungen von Clusterübergreifenden Funktionsaufrufen

Tabellarische Funktionen oder Ansichten können über Cluster hinweg referenziert werden. Folgende Einschränkung gilt:

1. Die Remotefunktion muss das tabellentakuläre Schema zurückgeben. Auf Skalarfunktionen kann nur im selben Cluster zugegriffen werden.
2. Die Remotefunktion kann nur skalare Parameter akzeptieren. Auf Funktionen, die ein oder mehrere Tabellenargumente abrufen, kann nur im gleichen Cluster zugegriffen werden.
3. Das Schema der Remotefunktion muss bekannt und invariant ihrer Parameter sein (siehe auch [Clusterübergreifende Abfragen und Schemaänderungen](../concepts/crossclusterandschemachanges.md)).

Der folgende Clusterübergreifende Aufruf ist **gültig:**

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

Die folgende Abfrage ruft die `MyCalc`Remote-Skalafunktion auf.
Dies verstößt gegen die Regel #1, daher ist sie **nicht gültig:**

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

Die folgende Abfrage `MyCalc` ruft die Remotefunktion auf und stellt einen tabellarischen Parameter bereit.
Dies verstößt gegen die Regel #2, daher ist sie **nicht gültig:**

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

Die folgende Abfrage `SomeTable` ruft die Remotefunktion auf, `tablename`die eine Variableschemaausgabe basierend auf dem Parameter aufweist.
Dies verstößt gegen die Regel #3, daher ist sie **nicht gültig:**

Tabellarische `OtherDb`Funktion in:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

In der Standarddatenbank:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

Die folgende Abfrage `GetDataPivot` ruft remotefunktion auf, die variable Schemaausgabe basierend auf dem Data[(Pivot() Plugin](pivotplugin.md) hat dynamische Ausgabe hat.
Dies verstößt gegen die Regel #3, daher ist sie **nicht gültig:**

Tabellarische `OtherDb`Funktion in:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

Tabellarische Funktion in der Standarddatenbank:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>Anzeigen von Daten

Anweisungen, die Daten an den Client zurückgeben, werden implizit durch die Anzahl der `take` zurückgegebenen Datensätze begrenzt, auch wenn der Operator nicht speziell verwendet wird. Um diese Begrenzung aufzuheben, verwenden Sie die Clientanforderungsoption `notruncation` .

Um Daten in grafischer Form anzuzeigen, verwenden Sie den [Renderoperator](renderoperator.md).

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
