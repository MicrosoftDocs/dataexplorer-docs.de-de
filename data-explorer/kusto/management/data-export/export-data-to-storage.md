---
title: 'Exportieren von Daten in den Speicher: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt das Exportieren von Daten in den Speicher in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: fd0ac46aa0e2cf73cf0ee5a51359cd346bf1beda
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321555"
---
# <a name="export-data-to-storage"></a>Exportieren von Daten in den Speicher

Führt eine Abfrage aus und schreibt das erste Resultset in einen externen Speicher, der durch eine [Speicher Verbindungs Zeichenfolge](../../api/connection-strings/storage.md)angegeben wird.

**Syntax**

`.export`[ `async` ] [ `compressed` ] `to` *Outputdataformat* 
 `(` *storageconnectionstring* [ `,` ...] `)` [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *Abfrage*

**Argumente**

* `async`: Gibt an, dass der Befehl im asynchronen Modus ausgeführt wird, wenn angegeben.
  Weitere Informationen zum Verhalten in diesem Modus finden Sie unten.

* `compressed`: Wenn angegeben, werden die Ausgabe Speicher Artefakte als `.gz` Dateien komprimiert. Informationen `compressionType` zum Komprimieren von Parkett Dateien als Snappy finden Sie unter. 

* *Outputdataformat*: gibt das Datenformat der Speicher Artefakte an, die vom Befehl geschrieben wurden. Folgende Werte werden unterstützt: `csv` , `tsv` , `json` und `parquet` .

* *Storageconnectionstring*: gibt eine oder mehrere [Speicher Verbindungs](../../api/connection-strings/storage.md) Zeichenfolgen an, die angeben, in welchen Speicher die Daten geschrieben werden sollen. (Für skalierbare Schreibvorgänge können mehr als eine Speicher Verbindungs Zeichenfolge angegeben werden.) Jede dieser Verbindungs Zeichenfolgen muss die Anmelde Informationen angeben, die beim Schreiben in den Speicher verwendet werden sollen.
  Wenn Sie z. b. in Azure BLOB Storage schreiben, können die Anmelde Informationen der Speicherkonto Schlüssel oder ein Shared Access Key (SAS) mit den Berechtigungen zum Lesen, schreiben und Auflisten von BLOB sein.

> [!NOTE]
> Es wird dringend empfohlen, Daten in den Speicher zu exportieren, der sich in derselben Region wie der Kusto-Cluster selbst befindet. Dies schließt Daten ein, die exportiert werden, damit Sie an einen anderen clouddienst in anderen Regionen übertragen werden können. Schreibvorgänge sollten lokal erfolgen, während Lesevorgänge Remote erfolgen können.

* *PropertyName* / *PropertyValue*: NULL oder mehr optionale Export Eigenschaften:

|Eigenschaft        |type    |Beschreibung                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |Die Größenbeschränkung in Bytes eines einzelnen Speicher Artefakts, das geschrieben wird (vor der Komprimierung). Der zulässige Bereich beträgt 100 MB (Standard) 1 GB.|
|`includeHeaders`|`string`|Für `csv` / `tsv` die Ausgabe steuert die Generierung von Spalten Headern. Kann eine der `none` (standardmäßigen, keine Header Zeilen ausgegeben), `all` (geben Sie eine Kopfzeile in jedem Speicher Element aus) oder (geben Sie `firstFile` nur eine Kopfzeile in das erste Speicher Element ein).|
|`fileExtension` |`string`|Gibt den "Extension"-Teil des Speicher Artefakts an (z `.csv` `.tsv` . b. oder). Wenn die Komprimierung verwendet wird, `.gz` wird ebenfalls angehängt.|
|`namePrefix`    |`string`|Gibt ein Präfix an, das den einzelnen generierten Speicher Artefaktnamen hinzugefügt wird. Wenn nicht angegeben, wird ein zufälliges Präfix verwendet.       |
|`encoding`      |`string`|Gibt an, wie der Text codiert werden soll: `UTF8NoBOM` (Standard) oder `UTF8BOM` . |
|`compressionType`|`string`|Gibt den zu verwendenden Komprimierungstyp an. Mögliche Werte sind `gzip` oder `snappy`. Der Standardwert ist `gzip`. `snappy` kann (optional) für das Format verwendet werden `parquet` . |
|`distribution`   |`string`  |Verteilungs Hinweis ( `single` , `per_node` , `per_shard` ). Wenn der Wert entspricht `single` , wird ein einzelner Thread in den Speicher geschrieben. Andernfalls schreibt der Export von allen Knoten, die die Abfrage parallel ausführen. Siehe [Auswerten des Plug](../../query/evaluateoperator.md)-in-Operators Wird standardmäßig auf `per_shard` festgelegt.
|`distributed`   |`bool`  |Deaktivieren/aktivieren Sie den verteilten Export. Das Festlegen von auf false entspricht dem `single` Verteilungs Hinweis. Der Standardwert ist "True".
|`persistDetails`|`bool`  |Gibt an, dass der Befehl seine Ergebnisse beibehalten soll (siehe `async` Flag). Der Standardwert ist in asynchronen Ausführungen `true` , kann aber deaktiviert werden, wenn der Aufrufer die Ergebnisse nicht benötigt). Wird standardmäßig `false` in synchronen Ausführungen verwendet, kann aber auch in aktiviert werden. |
|`parquetRowGroupSize`|`int`  |Nur relevant, wenn das Datenformat "Parkett" ist. Steuert die Zeilen Gruppengröße in den exportierten Dateien. Die Standardzeilen Gruppengröße beträgt 100000 Datensätze.|

**Ergebnisse**

Die Befehle gibt eine Tabelle zurück, in der die generierten Speicher Artefakte beschrieben werden.
Jeder Datensatz beschreibt ein einzelnes Element und enthält den Speicherpfad zum Element und die Anzahl der Datensätze, die es enthält.

|`Path`|Numrecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**Asynchroner Modus**

Wenn das- `async` Flag angegeben wird, wird der Befehl im asynchronen Modus ausgeführt.
In diesem Modus gibt der Befehl sofort eine Vorgangs-ID zurück, und der Datenexport wird im Hintergrund fortgesetzt, bis der Vorgang abgeschlossen ist. Die vom Befehl zurückgegebene Vorgangs-ID kann verwendet werden, um den Fortschritt und letztendlich seine Ergebnisse über die folgenden Befehle zu verfolgen:

* [`.show operations`](../operations.md#show-operations): Nachverfolgen des Fortschritts.
* [`.show operation details`](../operations.md#show-operation-details): Vervollständigungs Ergebnisse erhalten.

Nach erfolgreichem Abschluss können Sie die Ergebnisse beispielsweise mithilfe von abrufen:

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**Beispiele** 

In diesem Beispiel führt Kusto die Abfrage aus und exportiert dann das erste Recordset, das von der Abfrage erzeugt wird, in ein oder mehrere komprimierte CSV-BLOB.
Spaltennamen Bezeichnungen werden als erste Zeile für jedes BLOB hinzugefügt.

```kusto 
.export
  async compressed
  to csv (
    h@"https://storage1.blob.core.windows.net/containerName;secretKey",
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey"
  ) with (
    sizeLimit=100000,
    namePrefix=export,
    includeHeaders=all,
    encoding =UTF8NoBOM
  )
  <| myLogs | where id == "moshe" | limit 10000
```

## <a name="failures-during-export-commands"></a>Fehler beim Exportieren von Befehlen

Export Befehle können während der Ausführung transitiv fehlschlagen. Der [fortlaufende Export](continuous-data-export.md) führt den Befehl automatisch erneut aus. Reguläre Export Befehle ([Exportieren in den Speicher](export-data-to-storage.md), [Exportieren in eine externe Tabelle](export-data-to-an-external-table.md)) führen keine Wiederholungen aus.

*  Wenn der Export Befehl fehlschlägt, werden Artefakte, die bereits in den Speicher geschrieben wurden, nicht gelöscht. Diese Artefakte verbleiben im Speicher. Wenn der Befehl fehlschlägt, gehen Sie davon aus, dass der Export unvollständig ist, auch wenn einige Artefakte geschrieben wurden. 
* Die beste Methode zum Nachverfolgen der Ausführung des Befehls und der Elemente, die nach erfolgreichem Abschluss exportiert werden, ist die Verwendung der [`.show operations`](../operations.md#show-operations) [`.show operation details`](../operations.md#show-operation-details) Befehle und.

### <a name="storage-failures"></a>Speicherfehler

Standardmäßig werden Export Befehle so verteilt, dass möglicherweise viele gleichzeitige Schreibvorgänge im Speicher vorhanden sind. Die Verteilungsebene hängt vom Typ des Export Befehls ab:
* Die Standardverteilung für reguläre `.export` Befehle ist `per_shard` , d. h. alle [Blöcke](../extents-overview.md) , die Daten enthalten, die gleichzeitig in den Speicher geschrieben werden sollen. 
* Die Standardverteilung für den [Export in externe Tabellen](export-data-to-an-external-table.md) Befehle ist `per_node` , was bedeutet, dass die Parallelität die Anzahl der Knoten im Cluster ist.

Wenn die Anzahl von Blöcken/Knoten sehr groß ist, kann dies zu einer hohen Auslastung des Speichers führen, der zu einer Speicher Drosselung führt, oder zu vorübergehenden Speicherfehlern. Die folgenden Vorschläge können diese Fehler (nach Prioritäts Reihenfolge) beheben:

* Erhöhen Sie die Anzahl der Speicher Konten, die für den Export Befehl oder die [externe Tabellendefinition](../external-tables-azurestorage-azuredatalake.md) bereitgestellt werden (die Last wird gleichmäßig auf die Konten verteilt).
* Reduzieren Sie die Parallelität, indem Sie den Verteilungs Hinweis auf festlegen `per_node` (siehe Befehls Eigenschaften).
* Reduzieren Sie die Parallelität der Anzahl von Knoten, die exportiert werden, indem Sie die [Eigenschaft Client Anforderung](../../api/netfx/request-properties.md) `query_fanout_nodes_percent` auf die gewünschte Parallelität (Prozentsatz der Knoten) festlegen. Die-Eigenschaft kann als Teil der Export Abfrage festgelegt werden. Mit dem folgenden Befehl wird beispielsweise die Anzahl der Knoten, die gleichzeitig in den Speicher schreiben, auf 50% der Cluster Knoten beschränkt:

    ```kusto
    .export async  to csv
        ( h@"https://storage1.blob.core.windows.net/containerName;secretKey" ) 
        with
        (
            distribuion="per_node"
        ) 
        <| 
        set query_fanout_nodes_percent = 50;
        ExportQuery
    ```

* Beim Exportieren in eine partitionierte externe Tabelle kann durch das Festlegen der Eigenschaften die Parallelität `spread` / `concurrency` verringert werden (siehe Details in den [Befehls Eigenschaften](export-data-to-an-external-table.md#syntax).
* Wenn keines der oben genannten Aufgaben funktioniert, ist es auch möglich, die Verteilung vollständig zu deaktivieren, indem Sie die- `distributed` Eigenschaft auf false festlegen. Dies wird jedoch nicht empfohlen, da dies die Befehls Leistung erheblich beeinträchtigen kann.
