---
title: 'Richtlinie für die Daten Partitionierung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Richtlinie zur Daten Partitionierung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: 0bf2960d1bf585efc6b356a1b7075a27ca6616da
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188369"
---
# <a name="data-partitioning-policy"></a>Daten Partitionierungs Richtlinie

Die Partitionierungs Richtlinie definiert, ob und wie Blöcke [(datenshards)](../management/extents-overview.md) für eine bestimmte Tabelle partitioniert werden sollen.

Der Hauptzweck der Richtlinie besteht darin, die Leistung von Abfragen zu verbessern, die bekanntermaßen den Datensatz von Werten in den partitionierten Spalten eingrenzen, oder Aggregat/Join für eine Spalte mit hoher kardinalitätszeichenfolge. Die Richtlinie kann auch zu einer besseren Komprimierung der Daten führen.

> [!CAUTION]
> Es sind keine hart codierten Grenzwerte für die Anzahl der Tabellen festgelegt, für die die Richtlinie definiert werden kann. Allerdings erhöht jede zusätzliche Tabelle den Aufwand für den Hintergrunddaten Partitionierungs Prozess, der auf den Knoten des Clusters ausgeführt wird. Dies kann dazu führen, dass mehr Cluster Ressourcen verwendet werden. Weitere Informationen finden Sie unter [Überwachung](#monitoring) und [Kapazität](#capacity).

## <a name="partition-keys"></a>Partitionsschlüssel

Die folgenden Arten von Partitions Schlüsseln werden unterstützt.

|Variante                                                   |Spaltentyp |Partitionseigenschaften                    |Partitions Wert                                        |
|-------------------------------------------------------|------------|----------------------------------------|----------------------|
|[Hash](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[Einheitlicher Bereich](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>Hash Partitions Schlüssel

> [!NOTE]
> Anwenden eines Hash Partitions Schlüssels auf eine `string` Typspalte in einer Tabelle nur in den folgenden Instanzen:
> * Wenn die meisten Abfragen Gleichheits Filter ( `==` , `in()` ) verwenden.
> * Der Großteil der Abfragen aggregiert/verknüpft eine bestimmte `string` typisierte Spalte der *großen Dimension* (Kardinalität von 10 m oder höher), wie z. b `application_ID` ., `tenant_ID` oder `user_ID` .

* Eine Hash-Modulo-Funktion wird verwendet, um die Daten zu partitionieren.
* Alle homogenen (partitionierten) Blöcke, die derselben Partition angehören, werden demselben Datenknoten zugewiesen.
* Daten in homogenen (partitionierten) Blöcken werden nach dem Hash Partitions Schlüssel geordnet.
  * Sie müssen den Hash Partitions Schlüssel nicht in die [Richtlinie für die Zeilen Reihenfolge](roworderpolicy.md)einschließen, wenn eine für die Tabelle definiert ist.
* Abfragen, die die [shuffle-Strategie](../query/shufflequery.md)verwenden, und in denen der `shuffle key` , der in verwendet wird `join` , `summarize` oder `make-series` der Hash Partitions Schlüssel der Tabelle ist, erwarten eine bessere Leistung, da die Menge der Daten, die für die Umstellung auf Cluster Knoten erforderlich sind, erheblich reduziert wird.

#### <a name="partition-properties"></a>Partitionseigenschaften

* `Function`der Name einer Hash Modulo-Funktion, die verwendet werden soll.
  * Unterstützter Wert: `XxHash64` .
* `MaxPartitionCount`die maximale Anzahl der zu erstellenden Partitionen (das Modulo-Argument für die Hash Modulo-Funktion) pro Zeitraum.
  * Unterstützte Werte liegen im Bereich `(1,1024]` .
    * Der Wert muss wie folgt lauten:
      * Größer als die Anzahl der Knoten im Cluster
      * Kleiner als die Kardinalität der Spalte.
    * Je höher der Wert ist, desto größer ist der Aufwand für den Daten Partitionierungs Prozess auf den Knoten des Clusters, und je höher die Anzahl der Blöcke für jeden Zeitraum.
    * Es wird empfohlen, mit einem Wert von zu beginnen `256` .
      * Passen Sie den Wert nach Bedarf basierend auf den oben genannten Überlegungen oder basierend auf dem Vorteil der Abfrageleistung und dem Aufwand für die Partitionierung der Daten nach der Erfassung an.
* `Seed`der Wert, der zum randomialisieren des Hashwerts verwendet werden soll.
  * Der Wert muss eine positive ganze Zahl sein.
  * Der empfohlene Wert ist `1` . Dies ist die Standardeinstellung, sofern nicht angegeben.

#### <a name="example"></a>Beispiel

Ein Hash Partitions Schlüssel über eine `string` typisierte Spalte mit dem Namen `tenant_id` .
Sie verwendet die `XxHash64` Hash-Funktion mit dem `MaxPartitionCount` `256` und dem Standardwert `Seed` von `1` .

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>Einheitlicher Bereich DateTime-Partitions Schlüssel

> [!NOTE] 
> Anwenden eines unidirektionaldatetime-Partitions Schlüssels auf eine `datetime` typisierte Spalte in einer Tabelle, wenn in die Tabelle erfasste Daten wahrscheinlich nicht nach dieser Spalte geordnet sind.

In solchen Fällen kann es hilfreich sein, die Daten zwischen Blöcken neu zu mischen, sodass jeder Block am Ende Datensätze aus einem begrenzten Zeitraum einschließt. Dies führt dazu, dass Filter für diese `datetime` Spalte zur Abfragezeit effektiver werden.

Die verwendete Partitions Funktion ist [bin_at ()](../query/binatfunction.md) und kann nicht angepasst werden.

#### <a name="partition-properties"></a>Partitionseigenschaften

* `RangeSize`eine `timespan` skalare Konstante, die die Größe der einzelnen DateTime-Partitionen angibt.
  Es wird empfohlen, Folgendes zu tun:
  * Beginnen Sie mit dem Wert `1.00:00:00` (ein Tag).
  * Legen Sie einen Wert kürzer als den Wert fest, da dies dazu führen kann, dass die Tabelle eine große Anzahl kleiner Blöcke aufweist, die nicht zusammengeführt werden können.
* `Reference`eine `datetime` skalare Konstante, die einen festgelegten Zeitpunkt angibt, gemäß dem DateTime-Partitionen ausgerichtet werden.
  * Wir empfehlen Ihnen, mit zu beginnen `1970-01-01 00:00:00` .
  * Wenn Datensätze vorhanden sind, in denen der DateTime-Partitions Schlüssel `null` Werte aufweist, wird der Partitions Wert auf den Wert von festgelegt `Reference` .

#### <a name="example"></a>Beispiel

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

* **Partitionkeys**:
  * Eine Auflistung von [Partitions Schlüsseln](#partition-keys) , die definieren, wie die Daten in der Tabelle partitioniert werden.
  * Eine Tabelle kann über bis zu `2` Partitions Schlüssel verfügen, mit einer der folgenden Optionen.
    * Ein [Hash Partitions Schlüssel](#hash-partition-key).
    * Ein [einheitlicher Bereich der DateTime-Partitions Taste](#uniform-range-datetime-partition-key).
    * Ein [Hash Partitions Schlüssel](#hash-partition-key) und ein [einheitlicher Bereich DateTime-Partitions Schlüssel](#uniform-range-datetime-partition-key).
  * Jeder Partitions Schlüssel verfügt über die folgenden Eigenschaften:
    * `ColumnName`: `string ` Der Name der Spalte, nach der die Daten partitioniert werden.
    * `Kind`: `string` -Die anzuwendende Daten Partitionierungs Art ( `Hash` oder `UniformRange` ).
    * `Properties`: `property bag` Definiert Parameter entsprechend der Partitionierung.

* **Effectivedatetime**:
  * Der UTC-DateTime-Wert, aus dem die Richtlinie gültig ist.
  * Diese Eigenschaft ist optional. Wenn Sie nicht angegeben ist, wird die Richtlinie für die Daten übernommen, die nach dem Anwenden der Richtlinie erfasst wurden.
  * Alle nicht homogenen (nicht partitionierten) Blöcke, die aufgrund von Beibehaltungs Dauer gelöscht werden können, werden vom Partitionierungs Prozess ignoriert, da deren Erstellungszeit 90% der effektiven vorläufigen Lösch Dauer der Tabelle vorausgeht.

### <a name="example"></a>Beispiel

Richtlinien Objekt für die Daten Partitionierung mit zwei Partitions Schlüsseln.
1. Ein Hash Partitions Schlüssel über eine `string` typisierte Spalte mit dem Namen `tenant_id` .
    - Sie verwendet die `XxHash64` Hash-Funktion mit dem Wert `MaxPartitionCount` 256 und dem Standardwert `Seed` `1` .
1. Ein einheitlicher DateTime Range-Partitions Schlüssel für eine `datetime` Typspalte mit dem Namen `timestamp` .
    - Es `datetime(1970-01-01)` wird als Bezugspunkt mit einer Größe von `1d` für jede Partition verwendet.

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1
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

### <a name="additional-properties"></a>Zusätzliche Eigenschaften

Die folgenden Eigenschaften können als Teil der Richtlinie definiert werden, sind jedoch optional, und es wird empfohlen, Sie nicht zu ändern.

* **Minrowzählperoperation**:
  * Mindestens das Ziel für die Summe der Zeilen Anzahl der Quell Blöcke eines einzelnen Daten Partitionierungs Vorgangs.
  * Diese Eigenschaft ist optional. Der Standardwert lautet `0`.

* **Maxrowzählperoperation**:
  * Maximales Ziel für die Summe der Zeilen Anzahl der Quell Blöcke eines einzelnen Daten Partitionierungs Vorgangs.
  * Diese Eigenschaft ist optional. Der Standardwert ist `0` mit einem Standardziel von 5 Millionen Datensätzen.
    * Sie können einen Wert kleiner als 5 Mio. festlegen, wenn Sie festzustellen, dass die Partitionierungs Vorgänge eine sehr große Menge an Arbeitsspeicher oder CPU pro Vorgang belegen. Weitere Informationen finden Sie unter [Überwachung](#monitoring).

## <a name="notes"></a>Hinweise

### <a name="the-data-partitioning-process"></a>Der Daten Partitionierungs Prozess

* Die Daten Partitionierung wird als nach dem Erfassungsprozess im Cluster ausgeführt.
  * Bei einer Tabelle, die kontinuierlich in erfasst wird, wird erwartet, dass Sie immer über ein "Fragment" von Daten verfügen, die noch partitioniert werden können (nicht homogene Blöcke).
* Die Daten Partitionierung wird nur für heiße Blöcke ausgeführt, unabhängig vom Wert der- `EffectiveDateTime` Eigenschaft in der Richtlinie.
  * Wenn die Partitionierung von kalten Blöcken erforderlich ist, müssen Sie die [Cachingrichtlinie](cachepolicy.md)vorübergehend anpassen.

#### <a name="monitoring"></a>Überwachung

Verwenden Sie den Befehl [. Show Diagnostics](../management/diagnostics.md#show-diagnostics) , um den Status oder den Status der Partitionierung in einem Cluster zu überwachen.

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

Die Ausgabe umfasst Folgendes:

  * `MinPartitioningPercentageInSingleTable`: Der minimale Prozentsatz partitionierter Daten in allen Tabellen, die über eine Richtlinie zur Daten Partitionierung im Cluster verfügen.
    * Wenn dieser Prozentsatz konstant weiterhin unter 90% liegt, sollten Sie die Partitions [Kapazität](partitioningpolicy.md#capacity)des Clusters auswerten.
  * `TableWithMinPartitioningPercentage`: Der voll qualifizierte Name der Tabelle, deren Partitionierungs Prozentsatz oben angezeigt wird.

Verwenden Sie [. Show-Befehle](commands.md) , um die Partitionierungs Befehle und deren Ressourcenverwendung zu überwachen. Beispiel:

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

#### <a name="capacity"></a>Capacity

* Der Daten Partitionierungs Prozess führt zur Erstellung von weiteren Erweiterungen. Der Cluster kann die [Zusammenführungs Kapazität für Blöcke](../management/capacitypolicy.md#extents-merge-capacity)allmählich erhöhen, sodass der Prozess zum Zusammenführen von [Blöcken](../management/extents-overview.md) fortgeführt werden kann.
* Wenn ein hoher Erfassungs Durchsatz oder eine ausreichend große Anzahl von Tabellen vorhanden ist, für die eine Partitionierungs Richtlinie definiert ist, kann der Cluster die [Partitions Kapazität des Extenders](../management/capacitypolicy.md#extents-partition-capacity)allmählich erhöhen, sodass [der Prozess der Partitionierung von Blöcken](#the-data-partitioning-process) fort bleiben kann.
* Um zu vermeiden, dass zu viele Ressourcen verbraucht werden, sind diese dynamischen Steigerungen begrenzt. Sie müssen Sie möglicherweise schrittweise und linear über die Obergrenze hinaus erhöhen, wenn Sie vollständig genutzt werden.
  * Wenn die Kapazität der Cluster Ressourcen erheblich zunimmt, können Sie den Cluster [up](../../manage-cluster-vertical-scaling.md) / entweder manuell oder durch Aktivieren der automatischen Skalierung horizontal hoch[skalieren](../../manage-cluster-horizontal-scaling.md).

### <a name="outliers-in-partitioned-columns"></a>Ausreißer in partitionierten Spalten

* Wenn ein Hash Partitions Schlüsselwerte enthält, die wesentlich häufiger sind als andere, z. b. eine leere Zeichenfolge oder ein generischer Wert (z. b. `null` oder `N/A` ) oder eine Entität darstellen (z. b.), die im DataSet `tenant_id` allgemeinerer ist, kann dies zu einer unausgeglichenen Verteilung der Daten über die Knoten des Clusters beitragen und die Abfrageleistung beeinträchtigen.
* Wenn ein einheitlicher Bereich für einen DateTime-Partitions Schlüssel einen ausreichenden Prozentsatz der Werte aufweist, die von der Mehrzahl der Werte in der Spalte "weit" sind (z. b. DateTime-Werte aus der entfernten Vergangenheit oder Zukunft), kann dies den mehr Aufwand des Daten Partitionierungs Vorgangs erhöhen und zu vielen kleinen Blöcken führen, die der Cluster nachverfolgen muss.

In beiden Fällen sollten Sie die Daten entweder "korrigieren" oder vor oder während der Erfassungs Zeit irrelevante Datensätze in den Daten herausfiltern, um den mehr Aufwand für die Daten Partitionierung im Cluster zu verringern. Verwenden Sie z. b. eine [Update Richtlinie](updatepolicy.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Befehle der Partitionierungs Richtlinien Steuerung](../management/partitioning-policy.md) , um Daten Partitionierungs Richtlinien für Tabellen zu verwalten.
