---
title: 'Partitions Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Partitions Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6f2628bf2391ccea53e7fe70c76c341dda7f4e9
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324566"
---
# <a name="partition-operator"></a>partition-Operator

Der Partitions Operator partitioniert seine Eingabe Tabelle gemäß den Werten der angegebenen Spalte in mehrere untergeordnete Tabellen, führt eine Unterabfrage für jede untergeordnete Tabelle aus und erstellt eine einzelne Ausgabe Tabelle, die die Gesamtmenge der Ergebnisse aller Unterabfragen ist. 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

## <a name="syntax"></a>Syntax

*T* `|` `partition` [*partitionparameters*] `by` *Spalte* `(` *contextualsubquery*`)`

*T* `|` `partition` [*partitionparameters*] `by` *Spalten* `{` *Unterabfrage*`}`

## <a name="arguments"></a>Argumente

* *T*: die tabellarische Quelle, deren Daten vom Operator verarbeitet werden sollen.

* *Column*: der Name einer Spalte in *T* , deren Werte bestimmen, wie die Eingabe Tabelle partitioniert werden soll. Siehe **Hinweise** weiter unten.

* *Contextualsubquery*: ein tabellarischer Ausdruck, bei dem es sich um die Quelle des `partition` Operators handelt, die für einen einzelnen *Schlüssel* Wert festgelegt ist.

* *Unterabfrage*: ein tabellarischer Ausdruck ohne Quelle. Der *Schlüssel* Wert kann über den-Befehl abgerufen werden `toscalar()` .

* *Partitionparameters*: NULL oder mehr (durch Leerzeichen getrennte) Parameter in Form von: *Name* `=` *value* , die das Verhalten des Operators steuern. Die folgenden Parameter werden unterstützt:

  |Name               |Werte         |BESCHREIBUNG|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |Wenn festgelegt ist, `true` wird die Quelle des Operators materialisiert `partition` (Standard: `false` ).|
  |`hint.concurrency`|*Number*|Gibt an, wie viele Partitionen parallel ausgeführt werden sollen. *Standard* Wert: 16.|
  |`hint.spread`|*Number*|Gibt an, wie die Partitionen zwischen Cluster Knoten verteilt werden sollen (z. b. Wenn N Partitionen vorhanden sind und der Verteilungs Hinweis auf p festgelegt ist, dann werden die N-Partitionen von p verschiedene Cluster Knoten gleichmäßig parallel/sequenziell in Abhängigkeit vom Parallelitäts Hinweis verarbeitet). *Standard* Wert: 1.|

## <a name="returns"></a>Gibt zurück

Der-Operator gibt eine Union der Ergebnisse der Anwendung der Unterabfrage auf jede Partition der Eingabedaten zurück.

**Hinweise**

* Der Partitions Operator ist zurzeit durch die Anzahl der Partitionen beschränkt.
  Es können bis zu 64 unterschiedliche Partitionen erstellt werden.
  Der Operator führt zu einem Fehler, wenn die Partitions Spalte (*Spalte*) mehr als 64 unterschiedliche Werte aufweist.

* Die Unterabfrage verweist implizit auf die Eingabe Partition (in der Unterabfrage ist kein "Name" für die Partition vorhanden). Wenn Sie in der Unterabfrage mehrmals auf die Eingabe Partition verweisen möchten, verwenden Sie den [as-Operator](asoperator.md), wie im folgenden **Beispiel: Partitions Verweis** weiter unten.

**Beispiel: Top-netsted Case**

In einigen Fällen ist es leistungsfähiger und einfacher, Abfragen mithilfe des Operators zu schreiben, `partition` anstatt den [ `top-nested` Operator](topnestedoperator.md) zu verwenden. im nächsten Beispiel wird eine Unterabfrage berechnet `summarize` und `top` für jeden der Zustände gestartet `W` : (Wyoming, Washington, West Virginia, Wisconsin)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|Zustand|Ereignisse|Verletzungs|
|---|---|---|---|
|Hagel|Wyoming|108|0|
|Hoher Wind|Wyoming|81|5|
|Winter Storm|Wyoming|72|0|
|Starker Schnee|Washingtons|82|0|
|Hoher Wind|Washingtons|58|13|
|Wildfire|Washingtons|29|0|
|Sturm|WEST VIRGINIA|180|1|
|Hagel|WEST VIRGINIA|103|0|
|Winter Wetter|WEST VIRGINIA|88|0|
|Sturm|Wisconsin|416|1|
|Winter Storm|Wisconsin|310|0|
|Hagel|Wisconsin|303|1|

**Beispiel: Abfragen von nicht überlappenden Daten Partitionen**

Manchmal ist es hilfreich (leistungsorientiert), eine komplexe Unterabfrage über nicht überlappende Daten Partitionen in einem Map/Reduce-Stil auszuführen. Das folgende Beispiel zeigt, wie eine manuelle Verteilung von Aggregationen über 10 Partitionen erstellt wird.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|Coop-Beobachter|3039|

**Beispiel: Abfragezeit Partitionierung**

Das folgende Beispiel zeigt, wie die Abfrage in N = 10 Partitionen partitioniert werden kann, wobei jede Partition ihre eigene Anzahl berechnet und später in totalCount zusammengefasst wird.

<!-- csl: https://help.kusto.windows.net/Samples -->
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


**Beispiel: Partitions Verweis**

Im folgenden Beispiel wird gezeigt, wie der [as-Operator](asoperator.md) verwendet werden kann, um jeder Daten Partition einen "Namen" zuzuweisen und diesen Namen dann in der Unterabfrage wiederzuverwenden:

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**Beispiel: komplexe Unterabfrage, die durch einen Funktions aufrub ausgeblendet wird**

Das gleiche Verfahren kann mit wesentlich komplexeren Unterabfragen angewendet werden. Um die Syntax zu vereinfachen, können Sie die Unterabfrage in einem Funktions aufruppe einschließen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|Coop-Beobachter|3039|
