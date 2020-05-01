---
title: 'Exportieren von Daten in eine externe Tabelle: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie Daten in eine externe Tabelle in Azure Daten-Explorer exportieren.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 56150c480d0d5ecfd4d428e51f7bdb4b68e36b0c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617696"
---
# <a name="export-data-to-an-external-table"></a>Exportieren von Daten in eine externe Tabelle

Sie können Daten exportieren, indem Sie eine [externe Tabelle](../externaltables.md) definieren und Daten in diese exportieren.
Die Tabellen Eigenschaften werden beim [Erstellen der externen Tabelle](../externaltables.md#create-or-alter-external-table)angegeben. Daher müssen Sie die Eigenschaften der Tabelle nicht in den Export Befehl einbetten. Der Export-Befehl verweist anhand des Namens auf die externe Tabelle.
Zum Exportieren von Daten ist die [Berechtigung Datenbankadministrator](../access-control/role-based-authorization.md)erforderlich.

> [!NOTE] 
> * Das Exportieren in eine externe Tabelle `impersonate` mit Verbindungs Zeichenfolge wird derzeit nicht unterstützt.

**Syntax:**

`.export`[`async`] `to` `table` *Externaltablename* <br>
[`with` `(` *PropertyName* `=` *PropertyValue*`,`... `)`] <| *Abfrage*

**Ausgabe:**

|Output-Parameter |type |BESCHREIBUNG
|---|---|---
|Externaltablename  |String |Der Name der externen Tabelle.
|`Path`|String|Ausgabepfad.
|Numrecords|String| Anzahl der Datensätze, die in den Pfad exportiert werden.

**Hinweise:**
* Das Export Abfrage-Ausgabe Schema muss mit dem Schema der externen Tabelle, einschließlich aller von den Partitionen definierten Spalten, identisch sein. Wenn die Tabelle beispielsweise nach *DateTime*partitioniert wird, muss das Abfrageausgabe Schema eine timestamp-Spalte enthalten, die mit dem in der Definition der externen Tabelle definierten *timestampcolumnname* übereinstimmt.

* Es ist nicht möglich, die Eigenschaften externer Tabellen mit dem Export-Befehl zu überschreiben.
 Beispielsweise können Sie keine Daten im Parkett Format in eine externe Tabelle exportieren, deren Datenformat CSV ist.

* Die folgenden Eigenschaften werden als Teil des Export-Befehls unterstützt. Weitere Informationen finden Sie im Abschnitt [Exportieren in den Speicher](export-data-to-storage.md) : 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* Wenn die externe Tabelle partitioniert ist, werden exportierte Artefakte gemäß den Partitions Definitionen, wie im [Beispiel](#partitioned-external-table-example)gezeigt, in ihre jeweiligen Verzeichnisse geschrieben. 

* Die Anzahl der pro Partition geschriebenen Dateien hängt von folgendem ab:
   * Wenn die externe Tabelle nur DateTime-Partitionen oder überhaupt keine Partitionen enthält, sollte die Anzahl der geschriebenen Dateien (für jede Partition, falls vorhanden) die Anzahl der Knoten im Cluster betragen (oder mehr, wenn `sizeLimit` erreicht wird). Dies liegt daran, dass der Export Vorgang verteilt ist, sodass alle Knoten im Cluster gleichzeitig exportiert werden. 
   Um die Verteilung zu deaktivieren, sodass nur ein einziger Knoten die Schreibvorgänge ausführt `distributed` , legen Sie auf false fest. Dadurch werden weniger Dateien erstellt, die Exportleistung wird jedoch verringert.

   * Wenn die externe Tabelle eine Partition durch eine Zeichen folgen Spalte enthält, muss die Anzahl der exportierten Dateien eine einzelne Datei pro Partition (oder mehr, `sizeLimit` wenn erreicht wird) sein. Alle Knoten sind immer noch Teil des Exports (der Vorgang ist verteilt), jede Partition wird jedoch einem bestimmten Knoten zugewiesen. Wenn `distributed` in diesem Fall auf false festgelegt wird, bewirkt dies, dass nur ein einziger Knoten den Export ausführt, aber das Verhalten bleibt unverändert (eine einzelne Datei, die pro Partition geschrieben wird).

## <a name="examples"></a>Beispiele

### <a name="non-partitioned-external-table-example"></a>Beispiel für eine nicht partitionierte externe Tabelle

Externalblob ist eine nicht partitionierte externe Tabelle. 
```kusto
.export to table ExternalBlob <| T
```

|Externaltablename|`Path`|Numrecords|
|---|---|---|
|Externalblob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>Beispiel für eine partitionierte externe Tabelle

Partitionedexternalblob ist eine externe Tabelle, die wie folgt definiert wird: 

```kusto
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

```kusto
.export to table PartitionedExternalBlob <| T
```

|Externaltablename|`Path`|Numrecords|
|---|---|---|
|Externalblob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|Externalblob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

Wenn der Befehl asynchron ausgeführt wird (mithilfe des `async` Schlüssel Worts), ist die Ausgabe mit dem Befehl [Vorgangs Details anzeigen](../operations.md#show-operation-details) verfügbar.
