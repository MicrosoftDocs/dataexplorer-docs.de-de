---
title: Verwenden von LightIngest zum Erfassen von Daten in Azure Data Explorer
description: Hier erhalten Sie Informationen zu LightIngest, einem Befehlszeilen-Hilfsprogramm für die Ad-hoc-Datenerfassung in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/28/2020
ms.openlocfilehash: 1825ef642e5427df58800c8d6a71f75ff484bcf5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872640"
---
# <a name="use-lightingest-to-ingest-data-to-azure-data-explorer"></a>Verwenden von LightIngest zum Erfassen von Daten in Azure Data Explorer
 
LightIngest ist ein Befehlszeilen-Hilfsprogramm für die Ad-hoc-Datenerfassung in Azure Data Explorer. Das Hilfsprogramm kann Quelldaten aus einem lokalen Ordner oder aus einem Azure Blob Storage-Container abrufen.
LightIngest ist besonders bei der Erfassung großer Datenmengen hilfreich, da die Erfassung zeitlich nicht beschränkt ist. LightIngest ist außerdem nützlich, wenn Sie Datensätze später anhand des Erstellungszeitpunkts und nicht anhand des Erfassungszeitpunkts abfragen möchten.

## <a name="prerequisites"></a>Voraussetzungen

* LightIngest: Herunterladen des Programms als Teil des [NuGet-Pakets „Microsoft.Azure.Kusto.Tools“](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    :::image type="content" source="media/lightingest/lightingest-download-area.png" alt-text="LightIngest-Download":::

* WinRAR: Herunterladen des Programms unter [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Installieren von LightIngest

1. Navigieren Sie zu dem Speicherort auf dem Computer, in dem Sie LightIngest heruntergeladen haben.
1. Extrahieren Sie mithilfe von WinRAR das Verzeichnis *tools* auf dem Computer.

## <a name="run-lightingest"></a>Ausführen von LightIngest

1. Navigieren Sie zu dem extrahierten Verzeichnis *tools* auf dem Computer.
1. Löschen Sie die vorhandenen Speicherortinformationen in der Adressleiste.
    
    :::image type="content" source="kusto/tools/images/KustoTools-Lightingest/lightingest-locationbar.png" alt-text="Löschen vorhandener Speicherortinformationen für LightIngest":::

1. Geben Sie `cmd` ein, und drücken Sie die **EINGABETASTE**.
1. Geben Sie an der Eingabeaufforderung `LightIngest.exe` ein, gefolgt vom entsprechenden Befehlszeilenargument.

    > [!Tip]
    > Eine Liste der unterstützten Befehlszeilenargumente erhalten Sie durch Eingeben von `LightIngest.exe /help`.
    >
    > :::image type="content" source="media/lightingest/lightingest-cmd-line-help.png" alt-text="Befehlszeilenhilfe für LightIngest":::

1. Geben Sie `ingest-` gefolgt von der Verbindungszeichenfolge für den Azure Data Explorer-Cluster ein, in dem die Datenerfassung verwaltet wird.
    Setzen Sie die Verbindungszeichenfolge in doppelte Anführungszeichen, und befolgen Sie die [Spezifikation für Kusto-Verbindungszeichenfolgen](kusto/api/connection-strings/kusto.md).

    Beispiel:

    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="recommendations"></a>Empfehlungen

* Es wird empfohlen, für LightIngest den Erfassungsendpunkt unter `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` zu verwenden. Auf diese Weise kann die Erfassungslast im Azure Data Explorer-Dienst verwaltet werden, und nach vorübergehenden Fehlern kann problemlos eine Wiederherstellung durchgeführt werden. Sie können LightIngest jedoch auch so konfigurieren, dass das Programm direkt mit dem Engine-Endpunkt (`https://{yourClusterNameAndRegion}.kusto.windows.net`) verwendet wird.

   > [!NOTE]
   > Wenn Sie die Erfassung direkt mit dem Engine-Endpunkt durchführen, müssen Sie `ingest-` nicht einfügen. Es ist jedoch kein DM-Feature vorhanden, um die Engine zu schützen und die Erfolgsrate der Erfassung zu verbessern.

* Für eine optimale Erfassungsleistung ist die Rohdatengröße erforderlich, sodass in LightIngest die nicht komprimierte Größe der lokalen Dateien geschätzt werden kann. Allerdings kann die Größe der Rohdaten der komprimierten Blobs in LightIngest möglicherweise erst nach dem Herunterladen der Blobs korrekt geschätzt werden. Legen Sie daher bei der Erfassung von komprimierten Blobs die `rawSizeBytes`-Eigenschaft für die Blobmetadaten auf die nicht komprimierte Datengröße in Byte fest.

## <a name="command-line-arguments"></a>Befehlszeilenargumente

|Argumentname            |type     |Beschreibung       |Obligatorisch/Optional
|------------------------------|--------|----------|-----------------------------|
|                               |Zeichenfolge   |[Azure Data Explorer-Verbindungszeichenfolge](kusto/api/connection-strings/kusto.md), die den Kusto-Endpunkt angibt, an dem die Erfassung verarbeitet wird. Muss in doppelte Anführungszeichen eingeschlossen werden. | Obligatorisch.
|-database, -db          |Zeichenfolge  |Name der Azure Data Explorer-Zieldatenbank | Optional  |
|-table                  |Zeichenfolge  |Name der Azure Data Explorer-Zieltabelle | Obligatorisch. |
|-sourcePath, -source      |Zeichenfolge  |Pfad zu den Quelldateien oder zum Stamm-URI des Blobcontainers. Wenn sich die Daten in Blobs befinden, muss der Pfad den Speicherkontoschlüssel oder die SAS enthalten. Es empfiehlt sich, das Argument in doppelte Anführungszeichen einzuschließen. |Obligatorisch. |
|-prefix                  |Zeichenfolge  |Wenn sich die zu erfassenden Quelldaten in Blob Storage befinden, wird dieses URL-Präfix in allen Blobs gemeinsam genutzt, mit Ausnahme des Containernamens. <br>Wenn sich die Daten beispielsweise in `MyContainer/Dir1/Dir2` befinden, sollte das Präfix `Dir1/Dir2` lauten. Es empfiehlt sich, das Argument in doppelte Anführungszeichen einzuschließen. | Optional  |
|-pattern        |Zeichenfolge  |Muster, nach dem Quelldateien oder Blobs ausgewählt werden. Unterstützt Platzhalter. Beispiel: `"*.csv"`. Es empfiehlt sich, das Argument in doppelte Anführungszeichen einzuschließen. | Optional  |
|-zipPattern     |Zeichenfolge  |Regulärer Ausdruck, der zum Auswählen der zu erfassenden Dateien in einem ZIP-Archiv verwendet werden soll.<br>Alle anderen Dateien im Archiv werden ignoriert. Beispiel: `"*.csv"`. Es wird empfohlen, den regulären Ausdruck in doppelte Anführungszeichen einzuschließen. | Optional  |
|-format, -f           |Zeichenfolge  | Format der Quelldaten. Muss in einem der [unterstützten Formate](ingestion-supported-formats.md) vorliegen. | Optional  |
|-ingestionMappingPath, -mappingPath |Zeichenfolge  |Pfad zur lokalen Datei für die Erfassungsspaltenzuordnung. Obligatorisch für JSON- und Avro-Formate. Siehe [Datenzuordnungen](kusto/management/mappings.md). | Optional  |
|-ingestionMappingRef, -mappingRef  |Zeichenfolge  |Name einer Erfassungsspaltenzuordnung, die zuvor für die Tabelle erstellt wurde. Obligatorisch für JSON- und Avro-Formate. Siehe [Datenzuordnungen](kusto/management/mappings.md). | Optional  |
|-creationTimePattern      |Zeichenfolge  |Wenn das Argument festgelegt ist, wird es zum Extrahieren der CreationTime-Eigenschaft aus dem Datei- oder Blobpfad verwendet. Siehe [Erfassen von Daten mithilfe von `CreationTime`](#how-to-ingest-data-using-creationtime) |Optional  |
|-ignoreFirstRow, -ignoreFirst |bool    |Wenn das Argument festgelegt ist, wird der erste Datensatz jeder Datei bzw. jedes Blobs ignoriert (z. B. wenn die Quelldaten Header enthalten). | Optional  |
|-tag            |Zeichenfolge   |[Tags](kusto/management/extents-overview.md#extent-tagging), die den erfassten Daten zugeordnet werden. Mehrere Vorkommen sind zulässig. | Optional  |
|-dontWait           |bool     |Wenn das Argument auf „true“ festgelegt ist, wird nicht auf den Abschluss der Erfassung gewartet. Nützlich, wenn große Mengen an Dateien oder Blobs erfasst werden. |Optional  |
|-compression, -cr          |double |Komprimierungsverhältnis. Nützlich, wenn komprimierte Dateien oder Blobs erfasst werden, damit in Azure Data Explorer die Größe der Rohdaten bewertet werden kann. Wird als ursprüngliche Größe geteilt durch die komprimierte Größe berechnet. |Optional  |
|-limit , -l           |integer   |Wenn das Argument festgelegt ist, wird die Erfassung auf die ersten N Dateien beschränkt. |Optional  |
|-listOnly, -list        |bool    |Wenn das Argument festgelegt ist, werden nur die Elemente angezeigt, die für die Erfassung ausgewählt worden wären.| Optional  |
|-ingestTimeout   |integer  |Zeitlimit in Minuten für den Abschluss aller Erfassungsvorgänge. Der Standardwert lautet `60`.| Optional  |
|-forceSync        |bool  |Wenn das Argument festgelegt ist, wird die synchrone Erfassung erzwungen. Der Standardwert lautet `false`. |Optional  |
|-dataBatchSize        |integer  |Legt die Beschränkung der Gesamtgröße (MB, nicht komprimiert) für jeden Erfassungsvorgang fest. |Optional  |
|-filesInBatch            |integer |Legt die maximal zulässige Anzahl der Dateien oder Blobs für jeden Erfassungsvorgang fest. |Optional  |
|-devTracing, -trace       |Zeichenfolge    |Wenn das Argument festgelegt ist, werden Diagnoseprotokolle in ein lokales Verzeichnis geschrieben (standardmäßig `RollingLogs` im aktuellen Verzeichnis, kann durch Festlegen des Schalterwerts geändert werden). | Optional  |

## <a name="azure-blob-specific-capabilities"></a>Für Azure-Blobs spezifische Funktionen

Bei Verwendung mit Azure-Blobs werden in LightIngest bestimmte Blob-Metadateneigenschaften verwendet, um den Erfassungsprozess zu erweitern.

|Metadateneigenschaft                            | Verwendung                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Wenn die Eigenschaft festgelegt ist, wird die Größe der nicht komprimierten Daten interpretiert.                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Wird als UTC-Zeitstempel interpretiert. Wenn die Eigenschaft festgelegt ist, wird sie zum Überschreiben der Erstellungszeit in Kusto verwendet. Nützlich für Abgleichszenarien. |

## <a name="usage-examples"></a>Anwendungsbeispiele

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Ingestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="how-to-ingest-data-using-creationtime"></a>Erfassen von Daten mithilfe von CreationTime

Wenn Sie Verlaufsdaten aus einem vorhandenen System in Azure Data Explorer laden, erhalten alle Datensätze dasselbe Erfassungsdatum. Sie können das Argument `-creationTimePattern` verwenden, um die Partitionierung Ihrer Daten basierend auf der Erstellungszeit und nicht der Erfassungszeit zu ermöglichen. Mit dem Argument `-creationTimePattern` wird die `CreationTime`-Eigenschaft aus dem Datei- oder Blobpfad extrahiert. Das Muster muss nicht den gesamten Elementpfad darstellen, sondern lediglich den Abschnitt, der den zu verwendenden Zeitstempel enthält.

Die Argumentwerte müssen Folgendes enthalten:
* Konstanter Text direkt vor dem Zeitstempelformat, in einfache Anführungszeichen eingeschlossen (Präfix)
* Zeitstempelformat in der standardmäßigen [.NET-DateTime-Notation](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Konstanter Text direkt nach dem Zeitstempel (Suffix)

**Beispiele** 

* Ein Blobname, der Datum und Uhrzeit im folgenden Format enthält: `historicalvalues19840101.parquet`. (Der Zeitstempel setzt sich aus vier Ziffern für das Jahr sowie jeweils zwei Ziffern für Monat und Tag zusammen.) 
    
    Der Wert für das Argument `-creationTimePattern` ist Teil des Dateinamens: *'historicalvalues'yyyyMMdd'.parquet'* .

    ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Bei einem Blob-URI, der sich auf eine hierarchische Ordnerstruktur bezieht (etwa `https://storageaccount/container/folder/2002/12/01/blobname.extension`): 

    Der Wert für das Argument `-creationTimePattern` ist Teil der Ordnerstruktur: *'folder/'yyyy/MM/dd'/blob'* .

   ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'folder/'yyyy/MM/dd'/blob'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Erfassen von Blobs mithilfe eines Speicherkontoschlüssels oder eines SAS-Tokens

* Erfassen von 10 Blobs unter dem angegebenen Speicherkonto `ACCOUNT` im Ordner `DIR` unter dem Container `CONT` und in Übereinstimmung mit dem Muster `*.csv.gz`
* Ziel ist die Datenbank `DB`, Tabelle `TABLE`, und die Erfassungszuordnung `MAPPING`, die im Ziel vorab erstellt wurde.
* Das Tool wartet, bis die Erfassungsvorgänge abgeschlossen wurden.
* Beachten Sie die unterschiedlichen Optionen zum Angeben der Zieldatenbank und des Speicherkontoschlüssels im Vergleich zum SAS-Token

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Erfassen aller Blobs in einem Container, ohne Headerzeilen

* Erfassen aller Blobs unter dem angegebenen Speicherkonto `ACCOUNT` im Ordner `DIR1/DIR2` unter dem Container `CONT` und in Übereinstimmung mit dem Muster `*.csv.gz`
* Ziel ist die Datenbank `DB`, Tabelle `TABLE`, und die Erfassungszuordnung `MAPPING`, die im Ziel vorab erstellt wurde.
* Die Quellblobs enthalten eine Headerzeile. Daher wird das Tool angewiesen, den ersten Datensatz jedes Blobs zu ignorieren.
* Das Tool stellt die Daten für die Erfassung bereit und wartet nicht, bis die Erfassungsvorgänge abgeschlossen wurden.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Erfassen aller JSON-Dateien unter einem Pfad

* Erfassen aller Dateien unter dem Pfad `PATH` in Übereinstimmung mit dem Muster `*.json`
* Ziel ist die Datenbank `DB`, Tabelle `TABLE`, und die Erfassungszuordnung wird in der lokalen Datei `MAPPING_FILE_PATH` definiert.
* Das Tool stellt die Daten für die Erfassung bereit und wartet nicht, bis die Erfassungsvorgänge abgeschlossen wurden.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Erfassen von Dateien und Schreiben von Diagnosedateien für die Ablaufverfolgung

* Erfassen aller Dateien unter dem Pfad `PATH` in Übereinstimmung mit dem Muster `*.json`
* Ziel ist die Datenbank `DB`, Tabelle `TABLE`, und die Erfassungszuordnung wird in der lokalen Datei `MAPPING_FILE_PATH` definiert.
* Das Tool stellt die Daten für die Erfassung bereit und wartet nicht, bis die Erfassungsvorgänge abgeschlossen wurden.
* Diagnosedateien für die Ablaufverfolgung werden lokal in den Ordner `LOGS_PATH` geschrieben.

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
