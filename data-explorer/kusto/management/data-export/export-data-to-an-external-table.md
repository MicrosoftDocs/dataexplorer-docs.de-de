---
title: Exportieren von Daten in eine externe Tabelle - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Daten in eine externe Tabelle in Azure Data Explorer exportiert werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: e26d1ae163b69f3a04c52538c245ea446dc11199
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521640"
---
# <a name="export-data-to-an-external-table"></a>Exportieren von Daten in eine externe Tabelle

Sie können Daten exportieren, indem Sie eine [externe Tabelle](../externaltables.md) definieren und Daten in sie exportieren.
Die Tabelleneigenschaften werden beim [Erstellen der externen Tabelle](../externaltables.md#create-or-alter-external-table)angegeben, daher müssen Sie die Eigenschaften der Tabelle nicht in den Exportbefehl einbetten. Der Exportbefehl verweist auf die externe Tabelle nach Namen.
Für den Export von Daten [ist die Berechtigung des Datenbankadministrators](../access-control/role-based-authorization.md)erforderlich.

> [!NOTE] 
> * Das Exportieren in `impersonate` eine externe Tabelle mit Verbindungszeichenfolge wird derzeit nicht unterstützt.

**Syntax:**

`.export`[`async` `to` `table` ] *ExternalTableName* <br>
[`with` `(` *PropertyName* `=` *PropertyValue*`,`... `)`] <| *Abfrage*

**Ausgabe:**

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|---
|ExternalTableName  |String |Der Name der externen Tabelle.
|`Path`|String|Ausgabepfad.
|NumRecords|String| Anzahl der datensätze, die in den Pfad exportiert wurden.

**Hinweise:**
* Das Ausgabeschema für die Exportabfrage muss mit dem Schema der externen Tabelle übereinstimmen, einschließlich aller Spalten, die von den Partitionen definiert werden. Wenn die Tabelle beispielsweise von *DateTime*partitioniert wird, muss das Abfrageausgabeschema eine Timestamp-Spalte enthalten, die mit dem in der externen Tabellenpartitionierungsdefinition definierten *TimestampColumnName* übereinstimmt.

* Es ist nicht möglich, die eigenschaften externen Tabellen mithilfe des Befehls export zu überschreiben.
 Beispielsweise können Sie keine Daten im Parkettformat in eine externe Tabelle exportieren, deren Datenformat CSV ist.

* Die folgenden Eigenschaften werden als Teil des Exportbefehls unterstützt. Weitere Informationen finden Sie im Abschnitt [Export in storage:](export-data-to-storage.md) 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* Wenn die externe Tabelle partitioniert ist, werden exportierte Artefakte gemäß den Partitionsdefinitionen im [Beispiel](#partitioned-external-table-example)in ihre jeweiligen Verzeichnisse geschrieben. 

* Die Anzahl der pro Partition geschriebenen Dateien hängt von den folgenden Faktoren ab:
   * Wenn die externe Tabelle nur Datetime-Partitionen oder gar keine Partitionen enthält, sollte die Anzahl der geschriebenen Dateien (für jede Partition, falls vorhanden) um die Anzahl der Knoten im Cluster (oder mehr, wenn `sizeLimit` erreicht) liegen. Dies liegt daran, dass der Exportvorgang verteilt ist, sodass alle Knoten im Cluster gleichzeitig exportiert werden. 
   Um die Verteilung zu deaktivieren, sodass nur `distributed` ein einzelner Knoten die Schreibvorgänge ausführt, wird auf false gesetzt. Dadurch werden weniger Dateien erstellt, die Exportleistung jedoch verringert.

   * Wenn die externe Tabelle eine Partition durch eine Zeichenfolgenspalte enthält, sollte die Anzahl `sizeLimit` der exportierten Dateien eine einzelne Datei pro Partition (oder mehr, wenn erreicht) sein. Alle Knoten nehmen weiterhin am Export teil (der Vorgang wird verteilt), aber jede Partition wird einem bestimmten Knoten zugewiesen. Wenn `distributed` Sie in diesem Fall auf false setzen, führt nur ein einzelner Knoten dazu, den Export durchzuführen, aber das Verhalten bleibt gleich (eine einzelne Datei, die pro Partition geschrieben wird).

## <a name="examples"></a>Beispiele

### <a name="non-partitioned-external-table-example"></a>Beispiel für nicht partitionierte externe Tabellen

ExternalBlob ist eine nicht partitionierte externe Tabelle. 
```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName|`Path`|NumRecords|
|---|---|---|
|ExternalBlob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>Beispiel für partitionierte externe Tabellen

PartitionedExternalBlob ist eine externe Tabelle, die wie folgt definiert ist: 

```
.create external table PartitionedExternalBlob (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

```
.export to table PartitionedExternalBlob <| T
```

|ExternalTableName|`Path`|NumRecords|
|---|---|---|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

Wenn der Befehl asynchron ausgeführt wird `async` (mit dem Schlüsselwort), ist die Ausgabe mit dem Befehl Details des [Show-Vorgangs](../operations.md#show-operation-details) verfügbar.