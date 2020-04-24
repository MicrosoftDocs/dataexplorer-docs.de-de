---
title: Partitionsoperator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Partitionsoperator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a9ef31f68a989fffe42d9b54800e9305e51f4ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511372"
---
# <a name="partition-operator"></a>partition-Operator

Der Partitionsoperator partitioniert seine Eingabetabelle in mehrere Untertabellen entsprechend den Werten der angegebenen Spalte, führt eine Unterabfrage über jede Untertabelle aus und erzeugt eine einzelne Ausgabetabelle, die die Vereinigung der Ergebnisse aller Unterabfragen ist. 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

**Syntax**

*T* `|` T `partition` [*PartitionParameter*] `by` *Spalte* `(` *ContextualSubquery*`)`

*T* `|` T `partition` [*PartitionParameters*] `by` *Spaltenunterabfrage* *Column* `{``}`

**Argumente**

* *T*: Die Tabellarische Quelle, deren Daten vom Operator verarbeitet werden sollen.

* *Spalte*: Der Name einer Spalte in *T,* deren Werte bestimmen, wie die Eingabetabelle partitioniert werden soll. Siehe **Hinweise** unten.

* *ContextualSubquery*: Ein tabellarischer Ausdruck, `partition` dessen Quelle die Quelle des Operators ist und für einen einzelnen *Schlüsselwert* gilt.

* *Unterabfrage*: Ein tabellarischer Ausdruck ohne Quelle. Der *Schlüsselwert* kann `toscalar()` per Anruf abgerufen werden.

* *PartitionParameters*: Null oder mehr (raumgetrennte) Parameter in Form von: *Name* `=` *Value,* die das Verhalten des Operators steuern. Die folgenden Parameter werden unterstützt:

  |name               |Werte         |Beschreibung|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |Wenn auf `true` gesetzt wird, wird `partition` die Quelle `false`des Operators materialisiert (Standard: )|
  |`hint.concurrency`|*Anzahl*|Gibt das System an, wie `partition` viele gleichzeitige Unterabfragen des Operators parallel ausgeführt werden sollen. *Standard*: Anzahl der CPU-Kerne auf dem einzelnen Knoten des Clusters (2 bis 16).|
  |`hint.spread`|*Anzahl*|Gibt das System an, wie viele Knoten`partition` von der gleichzeitigen Unterabfrageausführung verwendet werden sollen. *Standard :* 1.|

**Rückgabe**

Der Operator gibt eine Vereinigung der Ergebnisse der Anwendung der Unterabfrage auf jede Partition der Eingabedaten zurück.

**Hinweise**

* Der Partitionsoperator ist derzeit durch die Anzahl der Partitionen begrenzt.
  Es können bis zu 64 verschiedene Partitionen erstellt werden.
  Der Operator gibt einen Fehler ab, wenn die Partitionsspalte (*Spalte*) mehr als 64 unterschiedliche Werte aufweist.

* Die Unterabfrage verweist implizit auf die Eingabepartition (es gibt keinen "Namen" für die Partition in der Unterabfrage). Um die Eingabepartition mehrmals innerhalb der Unterabfrage zu referenzieren, verwenden Sie den [as-Operator](asoperator.md), wie unter **Beispiel: Partitionsverweis** unten.

**Beispiel: top-nested-Fall**

In einigen Fällen - es ist leistungsfähiger und `partition` einfacher, Abfrage mit Operator statt mit `summarize` [ `top-nested` ](topnestedoperator.md) `top` Operator schreiben Das nächste Beispiel führt eine Unterabfrage Berechnung und für jeden von Staaten beginnend mit `W`: (WYOMING, WASHINGTON, WEST VIRGINIA, WISCONSIN)

```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|State|Ereignisse|Verletzungen|
|---|---|---|---|
|Hagel|Wyoming|108|0|
|Hoher Wind|Wyoming|81|5|
|Wintersturm|Wyoming|72|0|
|Schwerer Schnee|Washington|82|0|
|Hoher Wind|Washington|58|13|
|Wildfire|Washington|29|0|
|Sturm|WEST VIRGINIA|180|1|
|Hagel|WEST VIRGINIA|103|0|
|Winterwetter|WEST VIRGINIA|88|0|
|Sturm|Wisconsin|416|1|
|Wintersturm|Wisconsin|310|0|
|Hagel|Wisconsin|303|1|

**Beispiel: Abfrage nicht überlappender Datenpartitionen**

Manchmal ist es (perf-weise) nützlich, eine komplexe Unterabfrage über nicht überlappende Datenpartitionen in einem Map/Reduce-Stil auszuführen. Das folgende Beispiel zeigt, wie Sie eine manuelle Verteilung der Aggregation über 10 Partitionen erstellen.

```kusto
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    summarize Count=count() by Source 
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|`Source`|Anzahl|
|---|---|
|Ausgebildeter „Spotter“|12770|
|Strafverfolgungsbehörden|8570|
|Öffentlich|6157|
|Katastrophenschutz|4900|
|COOP-Beobachter|3039|

**Beispiel: Abfragezeitpartitionierung**

Das folgende Beispiel zeigt, wie die Abfrage in N=10-Partitionen partitioniert werden kann, wobei jede Partition ihre eigene Anzahl berechnet und alle später in TotalCount zusammengefasst wird.

```kusto
let N = 10;                 // Number of query-partitions
range p from 0 to N-1 step 1  // 
| partition by p            // Run the sub-query partitioned 
{
    StormEvents 
    | where hash(EventId, N) == toscalar(p) // Use toscalar() to fetch partition key value
    | summarize Count = count()
}
| summarize TotalCount=sum(Count) 
```

|TotalCount|
|---|
|59066|


**Beispiel: Partitionsverweis**

Das folgende Beispiel zeigt, wie man den [Operator as](asoperator.md) verwenden kann, um jeder Datenpartition einen "Namen" zu geben und diesen Namen dann in der Unterabfrage wiederzuverwenden:

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**Beispiel: komplexe Unterabfrage, die durch einen Funktionsaufruf ausgeblendet wird**

Die gleiche Technik kann mit viel komplexeren Unterabfragen angewendet werden. Um die Syntax zu vereinfachen, kann man die Unterabfrage in einem Funktionsaufruf umschließen:

```kusto
let partition_function = (T:(Source:string)) 
{
    T
    | summarize Count=count() by Source
};
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    invoke partition_function()
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|`Source`|Anzahl|
|---|---|
|Ausgebildeter „Spotter“|12770|
|Strafverfolgungsbehörden|8570|
|Öffentlich|6157|
|Katastrophenschutz|4900|
|COOP-Beobachter|3039|