---
title: Exportieren von Daten in den Speicher - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Daten in Azure Data Explorer gespeichert werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 12800955d1680a280aa4772db86d8b71e44e7089
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521572"
---
# <a name="export-data-to-storage"></a>Exportieren von Daten in die Speicherung

Führt eine Abfrage aus und schreibt das erste Resultset in einen externen Speicher, der durch eine [Speicherverbindungszeichenfolge](../../api/connection-strings/storage.md)angegeben wird.

**Syntax**

`.export`[`async`]`compressed` `to` [ ] *OutputDataFormat* 
 `(` *StorageConnectionString* [`,` ...] `)` `with` [ `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`] `<|` *Abfrage*

**Argumente**

* `async`: Wenn angegeben, gibt der Befehl an, dass er im asynchronen Modus ausgeführt wird.
  Weitere Informationen zum Verhalten in diesem Modus finden Sie weiter unten.

* `compressed`: Wenn angegeben, werden die Ausgabespeicherartefakte als `.gz` Dateien komprimiert. Siehe `compressionType` zum Komprimieren von Parkettdateien als schnippisch. 

* *OutputDataFormat*: Gibt das Datenformat der Speicherartefakte an, die durch den Befehl geschrieben wurden. Unterstützte Werte `csv`sind: `json`, `parquet` `tsv`, und .

* *StorageConnectionString*: Gibt eine oder mehrere [Speicherverbindungszeichenfolgen](../../api/connection-strings/storage.md) an, die angeben, in welchen Speicher die Daten geschrieben werden sollen. (Für skalierbare Schreibvorgänge kann mehr als eine Speicherverbindungszeichenfolge angegeben werden.) Jede solche Verbindungszeichenfolge muss die Anmeldeinformationen angeben, die beim Schreiben in den Speicher verwendet werden sollen.
  Beim Schreiben in Azure Blob Storage können die Anmeldeinformationen beispielsweise der Speicherkontoschlüssel oder ein freigegebener Zugriffsschlüssel (SAS) mit den Berechtigungen zum Lesen, Schreiben und Auflisten von Blobs sein.

> [!NOTE]
> Es wird dringend empfohlen, Daten in speicherweise zu exportieren, die sich in derselben Region wie der Kusto-Cluster selbst befinden. Dazu gehören Daten, die exportiert werden, damit sie an einen anderen Clouddienst in anderen Regionen übertragen werden können. Schreiben sollte lokal erfolgen, während Lesevorgänge aus der Ferne erfolgen können.

* *PropertyName*/*PropertyValue*: Keine oder mehr optionale Exporteigenschaften:

|Eigenschaft        |type    |BESCHREIBUNG                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |Die Größenbeschränkung in Bytes eines einzelnen Speicherartefakts, das geschrieben wird (vor der Komprimierung). Der zulässige Bereich beträgt 100 MB (Standard) bis 1 GB.|
|`includeHeaders`|`string`|Steuert `csv` / `tsv` für die Ausgabe die Generierung von Spaltenüberschriften. Kann eine `none` von sein (Standard; keine `all` Kopfzeilen emittiert), (geben `firstFile` Sie eine Kopfzeile in jedes Speicherartefakt) oder (geben Sie eine Kopfzeile nur in das erste Speicherartefakt).|
|`fileExtension` |`string`|Gibt den "Erweiterungs"-Teil des Speicherartefakts an (z. B. `.csv` oder `.tsv`). Wenn die Komprimierung `.gz` verwendet wird, wird ebenfalls angehängt.|
|`namePrefix`    |`string`|Gibt ein Präfix an, das jedem generierten Speicherartartartnamen hinzugefügt werden soll. Ein zufälliges Präfix wird verwendet, wenn es nicht angegeben wird.       |
|`encoding`      |`string`|Gibt an, wie der `UTF8NoBOM` Text codiert wird: (Standard) oder `UTF8BOM`. |
|`compressionType`|`string`|Gibt den Typ der zu verwendenden Komprimierung an. Mögliche Werte sind `gzip` oder `snappy`. Der Standardwert ist `gzip`. `snappy`(optional) für `parquet` das Format verwendet werden. |
|`distribution`   |`string`  |Verteilungshinweis`single` `per_node`( `per_shard`, , ). Wenn der `single`Wert gleich ist, wird ein einzelner Thread in den Speicher geschrieben. Andernfalls schreibt der Export von allen Knoten, die die Abfrage parallel ausführen. Siehe [Plugin-Operator auswerten](../../query/evaluateoperator.md). Der Standardwert lautet `per_shard`.
|`distributed`   |`bool`  |Deaktivieren/Aktivieren des verteilten Exports. Das Festlegen auf `single` false entspricht dem Verteilungshinweis. Der Standardwert ist "true".
|`persistDetails`|`bool`  |Gibt an, dass der Befehl `async` seine Ergebnisse beibehalten soll (siehe Flag). Standardwerte `true` für async-Läufe, können jedoch deaktiviert werden, wenn der Aufrufer die Ergebnisse nicht benötigt). Standardmäßig `false` in synchronen Ausführungen, kann aber auch in diesen aktiviert werden. |
|`parquetRowGroupSize`|`int`  |Relevant nur, wenn das Datenformat Parkett ist. Steuert die Zeilengruppengröße in den exportierten Dateien. Die Standardreihengruppengröße beträgt 100000 Datensätze.|

**Ergebnisse**

Die Befehle geben eine Tabelle zurück, die die generierten Speicherartefakte beschreibt.
Jeder Datensatz beschreibt ein einzelnes Artefakt und enthält den Speicherpfad zum Artefakt und die Darin enthaltene Menge an Datensätzen.

|`Path`|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**Asynchroner Modus**

Wenn `async` das Flag angegeben ist, wird der Befehl im asynchronen Modus ausgeführt.
In diesem Modus wird der Befehl sofort mit einer Vorgangs-ID zurückgegeben, und der Datenexport wird im Hintergrund bis zum Abschluss fortgesetzt. Die vom Befehl zurückgegebene Operation-ID kann verwendet werden, um den Fortschritt und die Ergebnisse über die folgenden Befehle zu verfolgen:

* [.show-Operationen](../operations.md#show-operations): Verfolgen Sie den Fortschritt.
* [.show-Vorgangsdetails](../operations.md#show-operation-details): Erhalten Sie Abschlussergebnisse.

Nach einem erfolgreichen Abschluss können Sie die Ergebnisse z. B. mit folgenden Informationen abrufen:

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**Beispiele** 

In diesem Beispiel führt Kusto die Abfrage aus und exportiert dann das erste von der Abfrage erzeugte Recordset in ein oder mehrere komprimierte CSV-Blobs.
Spaltennamenbeschriftungen werden als erste Zeile für jedes Blob hinzugefügt.

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

**Bekannte Probleme**

*Speicherfehler beim Exportbefehl*

Standardmäßig wird der Exportbefehl so verteilt, dass alle [Ausdehnungen,](../extents-overview.md) die Daten enthalten, die zum gleichzeitigen Exportieren von Schreibzugriff in den Speicher enthalten. Bei großen Exporten kann dies bei hohen Ausdehnungen zu einer hohen Speicherbelastung führen, die zu Speicherdrosselung oder vorübergehenden Speicherfehlern führt. In solchen Fällen wird empfohlen, die Anzahl der Speicherkonten zu erhöhen, die dem Exportbefehl zur Verfügung gestellt werden (die Last `per_node` wird auf die Konten verteilt) und/oder die Parallelität zu reduzieren, indem sie den Verteilungshinweis auf (siehe Befehlseigenschaften) festlegen. Eine vollständige Deaktivierung der Verteilung ist ebenfalls möglich, dies kann sich jedoch erheblich auf die Befehlsleistung auswirken.
 