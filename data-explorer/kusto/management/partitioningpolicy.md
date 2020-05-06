---
title: 'Richtlinie für die Daten Partitionierung (Vorschau): Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Richtlinie zur Daten Partitionierung (Vorschauversion) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 7440b42599d8559db547762daa5f8edcc24ea46f
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799440"
---
# <a name="data-partitioning-policy-preview"></a>Richtlinie für die Daten Partitionierung (Vorschau)

Die Partitionierungs Richtlinie definiert, ob und wie Blöcke [(datenshards)](../management/extents-overview.md) für eine bestimmte Tabelle partitioniert werden sollen.

> [!NOTE]
> Die Daten Partitionierungsfunktion befindet sich in der *Vorschau*Phase.

Der Hauptzweck der Richtlinie besteht darin, die Leistung von Abfragen zu verbessern, die bekanntermaßen zu einer kleinen Teilmenge von Werten in den partitionierten Spalten (n) und/oder Aggregat/Join für eine Spalte mit hoher kardinalitätszeichenfolge reduziert werden. Ein sekundärer potenzieller Vorteil ist eine bessere Komprimierung der Daten.

> [!WARNING]
> Es sind zwar keine hart codierten Grenzwerte für die Tabellen Menge festgelegt, für die die Richtlinie definiert ist, aber jede zusätzliche Tabelle erhöht den mehr Aufwand für den Hintergrunddaten Partitionierungs Prozess, der auf den Knoten des Clusters ausgeführt wird, und erfordert möglicherweise zusätzliche Ressourcen aus dem Cluster (siehe [Kapazität](#capacity)).

## <a name="partition-keys"></a>Partitionsschlüssel

Die folgenden Arten von Partitions Schlüsseln werden unterstützt:

|Variante                                                   |Spaltentyp |Partitionseigenschaften                    |Partitions Wert                                        |
|-------------------------------------------------------|------------|----------------------------------------|-------------------------------------------------------|
|[Hash](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[Einheitlicher Bereich](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>Hash Partitions Schlüssel

Das Anwenden eines Hash Partitions Schlüssels auf `string`eine typisierte-Spalte in einer Tabelle ist geeignet, wenn die *Mehrzahl* der Abfragen`==`Gleichheits `in()`Filter (,) `string`für eine bestimmte typisierte Spalte der *großen Dimension* (10 m oder höher) verwenden `application_ID`, `tenant_ID` wie `user_ID`z. b. oder.

* Eine Hash-Modulo-Funktion wird verwendet, um die Daten zu partitionieren.
* Alle *homogenen* (partitionierten) Blöcke, die derselben Partition angehören, werden demselben Datenknoten zugewiesen.
* Daten in *homogenen* (partitionierten) Blöcken werden nach dem Hash Partitions Schlüssel geordnet.
  * Es ist nicht erforderlich, den Partitions Schlüssel in eine [Richtlinie für die Zeilen Reihenfolge](roworderpolicy.md)einzubeziehen, wenn eine für die Tabelle definiert ist.
* Abfragen, die die [shuffle-Strategie](../query/shufflequery.md)verwenden und in denen `shuffle key` der in `join` `summarize` oder `make-series` der Hash Partitions Schlüssel der Tabelle verwendet wird, werden voraussichtlich besser ausgeführt, da die Menge an Daten, die für die Umstellung auf Cluster Knoten erforderlich ist, erheblich reduziert wird.

#### <a name="partition-properties"></a>Partitionseigenschaften

* `Function`der Name einer Hash Modulo-Funktion, die verwendet werden soll.
  * Unterstützter Wert `XxHash64`:.
* `MaxPartitionCount`die maximale Anzahl der zu erstellenden Partitionen (das Modulo-Argument für die Hash Modulo-Funktion) pro Zeitraum.
  * Unterstützt sind Werte im Bereich `(1,1024]`.
    * Der Wert muss wie folgt lauten:
      * Größer als die Anzahl der Knoten im Cluster
      * Kleiner als die Kardinalität der Spalte.
    * Je höher der Wert ist, desto größer ist der Aufwand für den Daten Partitionierungs Prozess auf den Knoten des Clusters, und je höher die Anzahl der Blöcke für jeden Zeitraum ist.
    * Ein empfohlener Wert, der `256`mit beginnt, ist.
      * Wenn dies erforderlich ist, kann es auf der Grundlage der oben genannten Überlegungen (in der Regel aufwärts) angepasst und/oder basierend auf der Messung des Vorteils der Abfrageleistung im Vergleich zum mehr Aufwand bei der Partitionierung der Daten nach der Erfassung genutzt werden.
* `Seed`der Wert, der zum randomialisieren des Hashwerts verwendet werden soll.
  * Der Wert muss eine positive ganze Zahl sein.
  * Der empfohlene (und standardmäßig, wenn nicht angegeben) Wert `1`ist.

#### <a name="example"></a>Beispiel

Im folgenden finden Sie einen Hash Partitions Schlüssel für `string`eine-typisierte `tenant_id`Spalte mit dem Namen.

Sie verwendet die `XxHash64` Hash `MaxPartitionCount` -Funktion mit `256`dem und dem Standard `Seed` Wert von. `1`

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

Das Anwenden eines Uniform Range-DateTime-Partitions `datetime`Schlüssels für eine typisierte Spalte in einer Tabelle eignet sich, wenn in der Tabelle erfasste Daten *wahrscheinlich* nicht nach dieser Spalte geordnet sind. In solchen Fällen kann es hilfreich sein, die Daten zwischen Blöcken neu zu mischen, sodass jeder Block am Ende Datensätze aus einem begrenzten Zeitraum einschließt, was dazu führt, dass Filter für `datetime` die Spalte zur Abfragezeit effizienter sind.

* Die verwendete Partitions Funktion ist [bin_at ()](../query/binatfunction.md) und kann nicht angepasst werden.

#### <a name="partition-properties"></a>Partitionseigenschaften

* `RangeSize`eine `timespan` skalare Konstante, die die Größe der einzelnen DateTime-Partitionen angibt.
* `Reference`eine `datetime` skalare Konstante vom Typ, die einen festgelegten Zeitpunkt angibt, gemäß dem DateTime-Partitionen ausgerichtet werden.
  * Wenn Datensätze vorhanden sind, in denen der DateTime- `null` Partitions Schlüsselwerte aufweist, wird der Partitions Wert auf den `Reference`Wert von festgelegt.

#### <a name="example"></a>Beispiel

Im folgenden ist ein einheitlicher DateTime Range-Partitions Schlüssel für `datetime`eine typisierte Spalte namens`timestamp`

Es wird `datetime(1970-01-01)` als Bezugspunkt mit einer Größe von `1d` für jede Partition verwendet.

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

Standardmäßig ist `null`die Daten Partitionierungs Richtlinie einer Tabelle. in diesem Fall werden die Daten in der Tabelle nicht partitioniert.

Die Richtlinie für die Daten Partitionierung verfügt über die folgenden Haupteigenschaften:

* **Partitionkeys**:
  * Eine Auflistung von [Partitions Schlüsseln](#partition-keys) , die definieren, wie die Daten in der Tabelle partitioniert werden.
  * Eine Tabelle kann über bis zu `2` Partitions Schlüssel verfügen, mit einer der folgenden drei Optionen:
    * 1 [Hash Partitions Schlüssel](#hash-partition-key).
    * 1 [einheitlicher Bereich DateTime-Partitions Schlüssel](#uniform-range-datetime-partition-key).
    * 1 [Hash Partitions Schlüssel](#hash-partition-key) und 1 [einheitlicher Bereich DateTime-Partitions Schlüssel](#uniform-range-datetime-partition-key).
  * Jeder Partitions Schlüssel verfügt über die folgenden Eigenschaften:
    * `ColumnName`: `string ` Der Name der Spalte, nach der die Daten partitioniert werden.
    * `Kind`: `string` -Die anzuwendende Daten Partitionierungs`Hash` Art `UniformRange`(oder).
    * `Properties`: `property bag` definiert Parameter entsprechend der Partitionierung.

* **Effectivedatetime**:
  * Der UTC-DateTime-Wert, aus dem die Richtlinie gültig ist.
  * Diese Eigenschaft ist *optional* . Wenn Sie nicht angegeben wird, wird die Richtlinie für die Daten wirksam, die nach dem Anwenden der Richtlinie erfasst wurden.
  * Alle nicht homogenen (nicht partitionierten) Blöcke, die gebunden werden, werden in Kürze aufgrund der Beibehaltung gelöscht (d. h., ihre Erstellungszeit vor 90% der effektiven vorläufigen Lösch Dauer der Tabelle) wird durch den Partitionierungs Prozess ignoriert.

### <a name="example"></a>Beispiel

Im folgenden finden Sie ein Richtlinien Objekt für die Daten Partitionierung mit zwei Partitions Schlüsseln:
1. Ein Hash Partitions Schlüssel über eine `string`typisierte Spalte mit `tenant_id`dem Namen.
    - Sie verwendet die `XxHash64` Hash-Funktion mit `MaxPartitionCount` dem Wert 256 und dem Standard `Seed` Wert. `1`
2. Ein einheitlicher DateTime Range-Partitions Schlüssel über `datetime`eine typisierte Spalte namens`timestamp`
    - Es wird `datetime(1970-01-01)` als Bezugspunkt mit einer Größe von `1d` für jede Partition verwendet.

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

Die folgenden Eigenschaften können als Teil der Richtlinie definiert werden, sind jedoch *optional* , und es wird nicht empfohlen, Sie zu ändern.

* **Minrowzählperoperation**:
  * Mindestens das Ziel für die Summe der Zeilen Anzahl der Quell Blöcke eines einzelnen Daten Partitionierungs Vorgangs.
  * Diese Eigenschaft ist *optional*, wobei der Standardwert ist `0`.

* **Maxrowzählperoperation**:
  * Maximales Ziel für die Summe der Zeilen Anzahl der Quell Blöcke eines einzelnen Daten Partitionierungs Vorgangs.
  * Diese Eigenschaft ist *optional*, wobei der Standardwert ist `0` (in diesem Fall ist ein Standardziel von 5 Millionen Datensätzen wirksam).

## <a name="notes"></a>Notizen

### <a name="the-data-partitioning-process"></a>Der Daten Partitionierungs Prozess

* Die Daten Partitionierung wird als nach dem Erfassungsprozess im Cluster ausgeführt.
  * Eine Tabelle, in der fortlaufend erfasst wird, erwartet, dass immer ein "Fragment" von Daten vorhanden ist, die noch partitioniert werden können (*nicht homogene* Blöcke).
* Die Daten Partitionierung wird nur für heiße Blöcke ausgeführt, unabhängig vom Wert der `EffectiveDateTime` -Eigenschaft in der Richtlinie.
  * Wenn die Partitionierung von kalten Blöcken erforderlich ist, müssen Sie die [Cachingrichtlinie](cachepolicy.md) entsprechend anpassen.

#### <a name="monitoring"></a>Überwachung

* Sie können den Status/Status der Partitionierung in einem Cluster überwachen, indem Sie den Befehl " [Diagnose" anzeigen](../management/diagnostics.md#show-diagnostics) :

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable,
          TableWithMinPartitioningPercentage
```

Die Ausgabe umfasst Folgendes:

  * `MinPartitioningPercentageInSingleTable`: der minimale Prozentsatz partitionierter Daten in allen Tabellen, die über eine Richtlinie zur Daten Partitionierung im Cluster verfügen.
      * Wenn dieser Prozentsatz konstant unter 90% liegt, sollten Sie die Partitionierungs Kapazität des Clusters auswerten (siehe [unten](partitioningpolicy.md#capacity)).
  * `TableWithMinPartitioningPercentage`: der voll qualifizierte Name der Tabelle, deren Partitionierungs Prozentsatz oben angezeigt wird.

#### <a name="capacity"></a>Capacity

* Da der Daten Partitionierungs Prozess zur Erstellung von größeren Blöcken führt, ist es möglicherweise erforderlich, (schrittweise und linear) die [Zusammenführungs Kapazität](../management/capacitypolicy.md#extents-merge-capacity) des Clusters zu vergrößern, sodass der Prozess zum Zusammenführen von [Blöcken](../management/extents-overview.md) in der Lage ist.
* Wenn dies erforderlich ist (beispielsweise bei einem hohen Erfassungs Durchsatz und/oder einer ausreichend großen Anzahl von Tabellen, die partitioniert werden müssen), kann die [Partitions Kapazität](../management/capacitypolicy.md#extents-partition-capacity) des Clusters (schrittweise und linear) erhöht werden, um eine größere Anzahl gleichzeitiger Partitionierungs Vorgänge zu ermöglichen.
  * Wenn das Erhöhen der Partitionierung einen erheblichen Anstieg der Verwendung der Cluster Ressourcen zur Folge hat, Skalieren Sie den Cluster entweder manuell oder durch Aktivieren der automatischen Skalierung zentral hoch-oder Herunterskalieren.

### <a name="outliers-in-partitioned-columns"></a>Ausreißer in partitionierten Spalten

* Wenn ein Hash Partitions Schlüssel einen ausreichenden Prozentsatz der Werte aufweist, die nicht ordnungsgemäß aufgefüllt werden (z. b. leer sind oder einen generischen Wert aufweisen), kann dies dazu beitragen, eine nicht ausgeglichene Verteilung der Daten auf die Knoten des Clusters zu haben.
* Wenn ein DateTime-Partitions Schlüssel für einen einheitlichen Bereich einen ausreichenden Prozentsatz an Werten aufweist, die "weit" von den meisten Werten in der Spalte liegen (z. b. DateTime-Werte aus der entfernten Vergangenheit oder Zukunft).

In beiden Fällen sollten Sie die Daten entweder "korrigieren" oder vor oder während der Erfassungs Zeit (z. b. mithilfe einer [Update Richtlinie](updatepolicy.md)) irrelevante Datensätze in den Daten herausfiltern, um den mehr Aufwand für die Daten Partitionierung im Cluster zu verringern.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Befehle der Partitionierungs Richtlinien Steuerung](../management/partitioning-policy.md) , um Daten Partitionierungs Richtlinien für Tabellen zu verwalten.
