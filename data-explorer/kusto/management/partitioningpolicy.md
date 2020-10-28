---
title: 'Richtlinie für die Daten Partitionierung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Richtlinie zur Daten Partitionierung in Azure Daten-Explorer und erläutert, wie Sie zur Verbesserung der Abfrageleistung verwendet werden kann.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: 1d2960e4fa8274d9e39aba935a49fd8d14d166e9
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902660"
---
# <a name="partitioning-policy"></a>Partitionierungsrichtlinie

Die Partitionierungs Richtlinie definiert, ob und wie Blöcke [(Daten-Shards)](../management/extents-overview.md) für eine bestimmte Tabelle partitioniert werden sollen.

Der Hauptzweck der Richtlinie besteht darin, die Leistung von Abfragen zu verbessern, die das DataSet eingrenzen. Beispielsweise Abfragen, die nach partitionierten Spalten filtern, ein Aggregat verwenden oder eine Verknüpfung für eine Spalte mit hoher kardinalitätszeichenfolge verwenden. Die Richtlinie kann auch zu einer besseren Datenkomprimierung führen.

> [!CAUTION]
> Es sind keine hart codierten Grenzwerte für die Anzahl der Tabellen festgelegt, für die die Partitionierungs Richtlinie definiert ist. Allerdings erhöht jede zusätzliche Tabelle den Aufwand für den Hintergrunddaten Partitionierungs Prozess, der auf den Knoten des Clusters ausgeführt wird. Das Hinzufügen von Tabellen kann dazu führen, dass mehr Cluster Ressourcen verwendet werden. Weitere Informationen finden Sie unter [Überwachung](#monitor-partitioning) und [Kapazität](#partition-capacity).

## <a name="partition-keys"></a>Partitionsschlüssel

Die folgenden Arten von Partitions Schlüsseln werden unterstützt.

|Art                                                   |Spaltentyp |Partitionseigenschaften                    |Partitions Wert                                        |
|-------------------------------------------------------|------------|----------------------------------------|----------------------|
|[Hash](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[Einheitlicher Bereich](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>Hash Partitions Schlüssel

> [!NOTE]
> Anwenden eines Hash Partitions Schlüssels auf eine `string` Typspalte in einer Tabelle nur in den folgenden Instanzen:
> * Wenn die meisten Abfragen Gleichheits Filter ( `==` , `in()` ) verwenden.
> * Der Großteil der Abfragen aggregiert/verknüpft eine bestimmte `string` typisierte Spalte der *großen Dimension* (Kardinalität von 10 m oder höher), wie z. b `application_ID` ., `tenant_ID` oder `user_ID` .

* Eine Hash-Modulo-Funktion wird verwendet, um die Daten zu partitionieren.
* Daten in homogenen (partitionierten) Blöcken werden nach dem Hash Partitions Schlüssel geordnet.
  * Sie müssen den Hash Partitions Schlüssel nicht in die [Richtlinie für die Zeilen Reihenfolge](roworderpolicy.md)einschließen, wenn eine für die Tabelle definiert ist.
* Abfragen, die die [shuffle-Strategie](../query/shufflequery.md)verwenden, und in denen das `shuffle key` in `join` `summarize` oder `make-series` der Hash Partitions Schlüssel der Tabelle verwendet wird, werden voraussichtlich besser ausgeführt, da die Menge der Daten, die für die Umstellung auf Cluster Knoten erforderlich sind, reduziert wird.

#### <a name="partition-properties"></a>Partitionseigenschaften

|Eigenschaft | Beschreibung | Unterstützte Werte| Empfohlener Wert |
|---|---|---|---|
| `Function` | Der Name einer zu verwendenden Hash Modulo-Funktion.| `XxHash64` | |
| `MaxPartitionCount` | Die maximale Anzahl der zu erstellenden Partitionen (das Modulo-Argument der Hash Modulo-Funktion) pro Zeitraum. | Im Bereich `(1,2048]` . <br>  Die Anzahl der Knoten im Cluster ist größer als fünfmal und kleiner als die Kardinalität der Spalte. |  Höhere Werte führen zu einem größeren mehr Aufwand für den Daten Partitionierungs Prozess auf den Knoten des Clusters und für jeden Zeitraum eine höhere Anzahl von Blöcken. Beginnen Sie bei Clustern mit weniger als 50 Knoten mit `256` . Passen Sie den Wert basierend auf diesen Überlegungen oder basierend auf dem Vorteil der Abfrageleistung und dem Aufwand für die Partitionierung der Daten nach der Erfassung an.
| `Seed` | Verwenden Sie, um den Hashwert zu randomialisieren. | Eine positive ganze Zahl | `1`, bei dem es sich auch um den Standardwert handelt. |
| `PartitionAssignmentMode` | Der Modus, der zum Zuweisen von Partitionen zu Knoten im Cluster verwendet wird. | `Default`: Alle homogenen (partitionierten) Blöcke, die derselben Partition angehören, werden demselben Knoten zugewiesen. <br> `Uniform`: Die Partitionswerte eines Blöcke werden ignoriert. Blöcke werden den Knoten des Clusters einheitlich zugewiesen. | Wenn Abfragen nicht mit dem Hash Partitions Schlüssel verknüpft oder aggregiert werden, verwenden Sie `Uniform` . Verwenden Sie andernfalls `Default`. |

#### <a name="hash-partition-key-example"></a>Beispiel für Hash Partitions Schlüssel

Ein Hash Partitions Schlüssel über eine `string` typisierte Spalte mit dem Namen `tenant_id` .
Sie verwendet die `XxHash64` Hash-Funktion mit dem `MaxPartitionCount` `256` und dem Standardwert `Seed` von `1` .

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1,
    "PartitionAssignmentMode": "Default"
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>Einheitlicher Bereich DateTime-Partitions Schlüssel

> [!NOTE] 
> Anwenden eines unidirektionaldatetime-Partitions Schlüssels auf eine `datetime` typisierte Spalte in einer Tabelle, wenn in die Tabelle erfasste Daten wahrscheinlich nicht nach dieser Spalte geordnet sind.

In diesen Fällen können Sie die Daten zwischen Blöcken neu mischen, sodass jeder Blockdaten Sätze aus einem begrenzten Zeitraum enthält. Dieser Prozess führt dazu, dass Filter für die `datetime` Spalte zur Abfragezeit effektiver werden.

Die verwendete Partitions Funktion ist [bin_at ()](../query/binatfunction.md) und kann nicht angepasst werden.

#### <a name="partition-properties"></a>Partitionseigenschaften

|Eigenschaft | Beschreibung | Empfohlener Wert |
|---|---|---|---|
| `RangeSize` | Eine `timespan` skalare Konstante, die die Größe der einzelnen DateTime-Partitionen angibt. | Beginnen Sie mit dem Wert `1.00:00:00` (ein Tag). Legen Sie keinen kürzeren Wert fest, da dies dazu führen kann, dass die Tabelle eine große Anzahl kleiner Blöcke aufweist, die nicht zusammengeführt werden können.
| `Reference` | Eine `datetime` skalare Konstante, die einen festgelegten Zeitpunkt angibt, gemäß dem DateTime-Partitionen ausgerichtet werden. | Beginnen Sie mit `1970-01-01 00:00:00`. Wenn Datensätze vorhanden sind, in denen der DateTime-Partitions Schlüssel `null` Werte aufweist, wird der Partitions Wert auf den Wert von festgelegt `Reference` . |

#### <a name="uniform-range-datetime-partition-example"></a>Beispiel für DateTime-Partition im einheitlichen Bereich

Der Code Ausschnitt zeigt einen einheitlichen DateTime Range-Partitions Schlüssel für eine `datetime` typisierte Spalte mit dem Namen `timestamp` .
Es `datetime(1970-01-01)` wird als Bezugspunkt mit einer Größe von `1d` für jede Partition verwendet.

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00"
  }
}
```

## <a name="the-policy-object"></a>Das Policy-Objekt

Standardmäßig ist die Daten Partitionierungs Richtlinie einer Tabelle `null` . in diesem Fall werden die Daten in der Tabelle nicht partitioniert.

Die Richtlinie für die Daten Partitionierung verfügt über die folgenden Haupteigenschaften:

* **Partitionkeys** :
  * Eine Auflistung von [Partitions Schlüsseln](#partition-keys) , die definieren, wie die Daten in der Tabelle partitioniert werden.
  * Eine Tabelle kann über bis zu `2` Partitions Schlüssel verfügen, mit einer der folgenden Optionen:
    * Ein [Hash Partitions Schlüssel](#hash-partition-key).
    * Ein [einheitlicher Bereich der DateTime-Partitions Taste](#uniform-range-datetime-partition-key).
    * Ein [Hash Partitions Schlüssel](#hash-partition-key) und ein [einheitlicher Bereich DateTime-Partitions Schlüssel](#uniform-range-datetime-partition-key).
  * Jeder Partitions Schlüssel verfügt über die folgenden Eigenschaften:
    * `ColumnName`: `string ` Der Name der Spalte, nach der die Daten partitioniert werden.
    * `Kind`: `string` -Die anzuwendende Daten Partitionierungs Art ( `Hash` oder `UniformRange` ).
    * `Properties`: `property bag` Definiert Parameter entsprechend der Partitionierung.

* **Effectivedatetime** :
  * Der UTC-DateTime-Wert, aus dem die Richtlinie gültig ist.
  * Diese Eigenschaft ist optional. Wenn Sie nicht angegeben ist, wird die Richtlinie für die Daten übernommen, die nach dem Anwenden der Richtlinie erfasst wurden.
  * Alle nicht homogenen (nicht partitionierten) Blöcke, die aufgrund von Beibehaltungs Dauer gelöscht werden können, werden vom Partitionierungs Prozess ignoriert. Die Blöcke werden ignoriert, weil ihre Erstellungszeit 90% des effektiven vorläufigen Lösch Zeitraums der Tabelle vorausgeht.
    > [!NOTE]
    > Sie können in der Vergangenheit einen DateTime-Wert festlegen und bereits erfasste Daten partitionieren. Diese Vorgehensweise kann jedoch die im Partitionierungs Prozess verwendeten Ressourcen erheblich erhöhen. 

### <a name="data-partitioning-example"></a>Beispiel zur Daten Partitionierung

Richtlinien Objekt für die Daten Partitionierung mit zwei Partitions Schlüsseln.
1. Ein Hash Partitions Schlüssel über eine `string` typisierte Spalte mit dem Namen `tenant_id` .
    * Sie verwendet die `XxHash64` Hash-Funktion mit dem Wert `MaxPartitionCount` 256 und dem Standardwert `Seed` `1` .
1. Ein einheitlicher DateTime Range-Partitions Schlüssel für eine `datetime` Typspalte mit dem Namen `timestamp` .
    * Es `datetime(1970-01-01)` wird als Bezugspunkt mit einer Größe von `1d` für jede Partition verwendet.

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1,
        "PartitionAssignmentMode": "Default"
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00"
      }
    }
  ]
}
```

## <a name="additional-properties"></a>Zusätzliche Eigenschaften

Die folgenden Eigenschaften können als Teil der Richtlinie definiert werden. Diese Eigenschaften sind optional, und es wird empfohlen, Sie nicht zu ändern.

|Eigenschaft | Beschreibung | Empfohlener Wert | Standardwert |
|---|---|---|---|
| **Minrowzählperoperation** |  Mindestens das Ziel für die Summe der Zeilen Anzahl der Quell Blöcke eines einzelnen Daten Partitionierungs Vorgangs. | | `0` |
| **Maxrowzählperoperation** |  Maximales Ziel für die Summe der Zeilen Anzahl der Quell Blöcke eines einzelnen Daten Partitionierungs Vorgangs. | Legen Sie einen Wert kleiner als 5 Mio. fest, wenn Sie festzustellen, dass die Partitionierungs Vorgänge eine große Menge an Arbeitsspeicher oder CPU pro Vorgang belegen. Weitere Informationen finden Sie unter [Überwachung](#monitor-partitioning). | `0`mit einem Standardziel von 5 Millionen Datensätzen. |

## <a name="the-data-partitioning-process"></a>Der Daten Partitionierungs Prozess

* Die Daten Partitionierung wird als nach dem Erfassungsprozess im Cluster ausgeführt.
  * Bei einer Tabelle, die kontinuierlich in erfasst wird, wird erwartet, dass Sie immer über ein "Fragment" von Daten verfügen, die noch partitioniert werden können (nicht homogene Blöcke).
* Die Daten Partitionierung wird nur für heiße Blöcke ausgeführt, unabhängig vom Wert der- `EffectiveDateTime` Eigenschaft in der Richtlinie.
  * Wenn die Partitionierung von kalten Blöcken erforderlich ist, müssen Sie die [Cachingrichtlinie](cachepolicy.md)vorübergehend anpassen.

## <a name="monitor-partitioning"></a>Monitor Partitionierung

Verwenden Sie den Befehl [. Show Diagnostics](../management/diagnostics.md#show-diagnostics) , um den Status oder den Status der Partitionierung in einem Cluster zu überwachen.

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

Die Ausgabe umfasst Folgendes:

  * `MinPartitioningPercentageInSingleTable`: Der minimale Prozentsatz partitionierter Daten in allen Tabellen, die über eine Richtlinie zur Daten Partitionierung im Cluster verfügen.
    * Wenn dieser Prozentsatz konstant weiterhin unter 90% liegt, sollten Sie die Partitions [Kapazität](partitioningpolicy.md#partition-capacity)des Clusters auswerten.
  * `TableWithMinPartitioningPercentage`: Der voll qualifizierte Name der Tabelle, deren Partitionierungs Prozentsatz oben angezeigt wird.

Verwenden Sie [. Show-Befehle](commands.md) , um die Partitionierungs Befehle und deren Ressourcenverwendung zu überwachen. Zum Beispiel:

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

## <a name="partition-capacity"></a>Partitions Kapazität

* Der Daten Partitionierungs Prozess führt zur Erstellung von weiteren Erweiterungen. Der Cluster kann die [Zusammenführungs Kapazität für Blöcke](../management/capacitypolicy.md#extents-merge-capacity)allmählich erhöhen, sodass der Prozess zum Zusammenführen von [Blöcken](../management/extents-overview.md) fortgeführt werden kann.
* Wenn ein hoher Erfassungs Durchsatz oder eine ausreichend große Anzahl von Tabellen vorhanden ist, für die eine Partitionierungs Richtlinie definiert ist, kann der Cluster die [Partitions Kapazität des Extenders](../management/capacitypolicy.md#extents-partition-capacity)allmählich erhöhen, sodass [der Prozess der Partitionierung von Blöcken](#the-data-partitioning-process) fort bleiben kann.
* Um zu vermeiden, dass zu viele Ressourcen verbraucht werden, sind diese dynamischen Steigerungen begrenzt. Sie müssen Sie möglicherweise schrittweise und linear über die Obergrenze hinaus erhöhen, wenn Sie vollständig genutzt werden.
  * Wenn die Kapazität der Cluster Ressourcen erheblich zunimmt, können Sie den Cluster [up](../../manage-cluster-vertical-scaling.md) / entweder manuell oder durch Aktivieren der automatischen Skalierung horizontal hoch[skalieren](../../manage-cluster-horizontal-scaling.md).

## <a name="outliers-in-partitioned-columns"></a>Ausreißer in partitionierten Spalten

* Die folgenden Situationen können zu einer unausgeglichenen Verteilung von Daten über die Knoten des Clusters und zum mindern der Abfrageleistung beitragen:
    * Wenn ein Hash Partitions Schlüsselwerte enthält, die wesentlich häufiger sind als andere, z. b. eine leere Zeichenfolge oder ein generischer Wert ( `null` z `N/A` . b. oder).
    * Die Werte stellen eine Entität dar (z `tenant_id` . b.), die im DataSet häufiger vorkommt.
* Wenn ein einheitlicher Bereich für einen DateTime-Partitions Schlüssel einen ausreichenden Prozentsatz der Werte aufweist, die von der Mehrzahl der Werte in der Spalte "weit" liegen, wird der Aufwand für den Daten Partitionierungs Prozess gesteigert und kann zu vielen kleinen Blöcken führen, die vom Cluster nachverfolgt werden müssen. Ein Beispiel für eine solche Situation sind DateTime-Werte aus der entfernten Vergangenheit oder Zukunft.

In beiden Fällen können Sie die Daten entweder "korrigieren" oder vor oder während der Erfassungs Zeit irrelevante Datensätze in den Daten herausfiltern, um den mehr Aufwand für die Daten Partitionierung im Cluster zu verringern. Verwenden Sie z. b. eine [Update Richtlinie](updatepolicy.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Befehle der Partitionierungs Richtlinien Steuerung](../management/partitioning-policy.md) , um Daten Partitionierungs Richtlinien für Tabellen zu verwalten.
