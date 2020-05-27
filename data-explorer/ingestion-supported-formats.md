---
title: Von Azure Data Explorer für die Erfassung unterstützte Datenformate.
description: Hier erfahren Sie mehr über die verschiedenen Daten- und Komprimierungsformate, die von Azure Data Explorer für die Erfassung unterstützt werden.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: c591c4f774f3af99004d398898b288256861bb75
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373722"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Von Azure Data Explorer für die Erfassung unterstützte Datenformate.

Bei der Datenerfassung werden Daten einer Tabelle hinzugefügt und für Abfragen in Azure Data Explorer verfügbar gemacht. Mit Ausnahme der Erfassung aus einer Abfrage müssen die Daten bei allen Erfassungsmethoden in einem der unterstützten Formate vorliegen. In der folgenden Tabelle sind die Formate aufgeführt und beschrieben, die von Azure Data Explorer für die Datenerfassung unterstützt werden.

|Format   |Durchwahl   |BESCHREIBUNG|
|---------|------------|-----------|
|Avro     |`.avro`     |Eine [Avro-Containerdatei](https://avro.apache.org/docs/current/). Die folgenden Codes werden unterstützt: `null` und `deflate`. (`snappy` wird derzeit nicht unterstützt.)|
|ApacheAvro|`.avro`    |Experimentelle native Implementierung für das [Avro](https://avro.apache.org/docs/current/)-Format mit Unterstützung für [logische Typen](https://avro.apache.org/docs/current/spec.html#Logical+Types) und für den Komprimierungscodec `snappy`.|
|CSV      |`.csv`      |Eine Textdatei mit kommagetrennten Werten (`,`). Weitere Informationen finden Sie unter [RFC 4180: _Common Format and MIME Type for Comma-Separated Values (CSV) Files_](https://www.ietf.org/rfc/rfc4180.txt) (RFC 4180: Allgemeines Format und MIME-Typ für CSV-Dateien).|
|JSON     |`.json`     |Eine Textdatei mit JSON-Objekten getrennt durch `\n` oder `\r\n`. Weitere Informationen finden Sie unter [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Eine Textdatei mit einem JSON-Array mit Eigenschaftenbehältern (die jeweils einen Datensatz darstellen) oder einer beliebigen Anzahl von durch Leerzeichen getrennten Eigenschaftenbehältern, `\n` oder `\r\n`. Jeder Eigenschaftenbehälter kann auf mehrere Zeilen verteilt sein. Dieses Format ist `JSON` vorzuziehen, es sei denn, bei den Daten handelt es sich nicht um Eigenschaftenbehälter.|
|ORC      |`.orc`      |Eine [ORC-Datei](https://en.wikipedia.org/wiki/Apache_ORC)|
|Parquet  |`.parquet`  |Eine [Parquet-Datei](https://en.wikipedia.org/wiki/Apache_Parquet)|
|PSV      |`.psv`      |Eine Textdatei mit durch senkrechte Striche getrennten Werten (<code>&#124;</code>).|
|RAW      |`.raw`      |Eine Textdatei, deren gesamter Inhalt ein einzelner Zeichenfolgenwert ist.|
|SCsv     |`.scsv`     |Eine Textdatei mit durch Semikolons getrennten Werten (`;`).|
|SOHsv    |`.sohsv`    |Eine Textdatei mit SOH-getrennten Werten. (SOH ist der ASCII-Codepunkt 1. Dieses Format wird von Hive in HDInsight verwendet.)|
|TSV      |`.tsv`      |Eine Textdatei mit durch Tabstopps getrennten Werten (`\t`).|
|TSVE     |`.tsv`      |Eine Textdatei mit durch Tabstopps getrennten Werten (`\t`). Als Escapezeichen wird ein umgekehrter Schrägstrich (`\`) verwendet.|
|TXT      |`.txt`      |Eine Textdatei mit durch `\n` getrennten Zeilen. Leere Zeilen werden übersprungen.|
|W3CLOGFILE |`.log`    |Vom W3C standardisiertes Format der [Webprotokolldatei](https://www.w3.org/TR/WD-logfile.html)|

## <a name="supported-data-compression-formats"></a>Unterstützte Datenkomprimierungsformate

Blobs und Dateien können mit einem der folgenden Komprimierungsalgorithmen komprimiert werden:

|Komprimierung|Durchwahl|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Geben Sie die Komprimierung an, indem Sie die Erweiterung dem Namen des Blobs oder der Datei anhängen.

Beispiel:
* `MyData.csv.zip` gibt an, dass ein Blob oder eine Datei als CSV formatiert und mit ZIP (Archiv oder einzelne Datei) komprimiert ist.
* `MyData.csv.gz` gibt an, dass ein Blob oder eine Datei als CSV formatiert und mit GZIP (Archiv oder einzelne Datei) komprimiert ist.

Blob- oder Dateinamen, die nicht die Formaterweiterungen, sondern nur die Komprimierung (z. B. `MyData.zip`) enthalten, werden ebenfalls unterstützt. In diesem Fall muss das Dateiformat als Erfassungseigenschaft angegeben werden, da es nicht abgeleitet werden kann.

> [!NOTE]
> Bei einigen Komprimierungsformaten wird die ursprüngliche Dateierweiterung in den komprimierten Datenstrom aufgenommen. Diese Erweiterung wird bei der Bestimmung des Dateiformats generell ignoriert. Kann das Dateiformat nicht anhand des Namens des (komprimierten) Blobs oder der (komprimierten) Datei bestimmt werden, muss es mithilfe der Erfassungseigenschaft `format` angegeben werden.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Datenerfassung](ingest-data-overview.md).
* Erfahren Sie mehr über die [Datenerfassungseigenschaften von Azure Data Explorer](ingestion-properties.md).
