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
ms.openlocfilehash: b470d017937ed6f2687016ab8a7cf53fed7b51ab
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614478"
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

|Eigenschaft        |type    |BESCHREIBUNG                                                                                                                |
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

|Pfad|Numrecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**Asynchroner Modus**

Wenn das- `async` Flag angegeben wird, wird der Befehl im asynchronen Modus ausgeführt.
In diesem Modus gibt der Befehl sofort eine Vorgangs-ID zurück, und der Datenexport wird im Hintergrund fortgesetzt, bis der Vorgang abgeschlossen ist. Die vom Befehl zurückgegebene Vorgangs-ID kann verwendet werden, um den Fortschritt und letztendlich seine Ergebnisse über die folgenden Befehle zu verfolgen:

* [. Show-Vorgänge](../operations.md#show-operations): Nachverfolgen des Fortschritts.
* [. Vorgangs Details anzeigen](../operations.md#show-operation-details): Vervollständigungs Ergebnisse erhalten.

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

#### <a name="known-issues"></a>Bekannte Probleme

**Fehler beim Export Befehl.**

* Der Export Befehl kann während der Ausführung transitiv fehlschlagen. Wenn der Export Befehl fehlschlägt, werden Artefakte, die bereits in den Speicher geschrieben wurden, nicht gelöscht. Diese Artefakte verbleiben im Speicher. Wenn der Befehl fehlschlägt, gehen Sie davon aus, dass der Export unvollständig ist, auch wenn einige Artefakte geschrieben wurden. Die beste Möglichkeit zum Nachverfolgen der Ausführung des Befehls und der Elemente, die nach erfolgreichem Abschluss exportiert werden, ist die Verwendung der Befehle [. Show Operations](../operations.md#show-operations) und [. Show Operation Details](../operations.md#show-operation-details) .

* Standardmäßig wird der Export Befehl so verteilt, dass alle [Blöcke](../extents-overview.md) , die Daten enthalten, die gleichzeitig in den Speicher geschrieben werden sollen, verteilt werden. Bei großen Exporten, wenn die Anzahl solcher Blöcke hoch ist, kann dies zu einer hohen Speicherauslastung führen, die zu einer Speicher Drosselung oder zu vorübergehenden Speicherfehlern führt. In solchen Fällen wird empfohlen, die Anzahl der Speicher Konten zu erhöhen, die für den Export Befehl bereitgestellt werden (die Last wird zwischen den Konten verteilt) und/oder um die Parallelität zu verringern, indem Sie den Verteilungs Hinweis auf festlegen `per_node` (siehe Befehls Eigenschaften). Die vollständige Deaktivierung der Verteilung ist ebenfalls möglich, dies kann sich jedoch erheblich auf die Befehls Leistung auswirken.
