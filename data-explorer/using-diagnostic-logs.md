---
title: Überwachen der Erfassung, Befehle und Abfragen von Azure Data Explorer mithilfe von Diagnoseprotokollen
description: Hier erfahren Sie, wie Sie Diagnoseprotokolle für Azure Data Explorer einrichten, um Erfassungsbefehle und Abfragevorgänge zu überwachen.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: 5142b6abfb5a7898fe58cd6264251e57dc95ae81
ms.sourcegitcommit: e37f52d6a4f6e782471b44ce21f978e2d83ffc28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98068703"
---
# <a name="monitor-azure-data-explorer-ingestion-commands-queries-and-tables-using-diagnostic-logs"></a>Überwachen der Erfassung, Befehle, Abfragen und Tabellen von Azure Data Explorer mithilfe von Diagnoseprotokollen

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. [Azure Monitor-Aktivitätsprotokolle](/azure/azure-monitor/platform/diagnostic-logs-overview) enthalten Daten zum Betrieb von Azure-Ressourcen. In Azure Data Explorer werden Diagnoseprotokolle verwendet, um Erkenntnisse zur Erfassung, zu Befehlen, Abfragen und Tabellen zu gewinnen. Sie können Vorgangsprotokolle in Azure Storage, Event Hub oder Log Analytics exportieren, um den Status von Erfassungen, Befehlen und Abfragen zu überwachen. Protokolle aus Azure Storage und Azure Event Hub können zur weiteren Analyse an eine Tabelle im Azure Data Explorer-Cluster weitergeleitet werden.

> [!IMPORTANT] 
> Diagnoseprotokolle können vertrauliche Daten enthalten. Schränken Sie die Berechtigungen des Protokollziels gemäß Ihren Überwachungsanforderungen ein. 

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/), falls Sie nicht über ein Azure-Abonnement verfügen.
* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
* Erstellen Sie einen [Cluster und eine Datenbank](create-cluster-database-portal.md).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Einrichten von Diagnoseprotokollen für einen Azure Data Explorer-Cluster

Mit Diagnoseprotokollen kann die Sammlung der folgenden Protokolldaten konfiguriert werden:

# <a name="ingestion"></a>[Erfassung](#tab/ingestion)

> [!NOTE]
> Erfassungsprotokolle werden für die Erfassung aus der Warteschlange am Erfassungsendpunkt unter Verwendung von SDKs, Datenverbindungen und Connectors unterstützt.
>
> Erfassungsprotokolle werden für die Streamingerfassung, die direkte Erfassung an der Engine, die Erfassung von Abfragen oder Befehle vom Typ „set-or-append“ nicht unterstützt.

> [!NOTE]
> Fehlerhafte Erfassungsprotokolle werden nur für den endgültigen Status eines Erfassungsvorgangs gemeldet. Dies steht im Gegensatz zur Metrik [Erfassungsergebnis](using-metrics.md#ingestion-metrics), die für intern wiederholte vorübergehende Fehler ausgegeben wird.

* **Erfolgreiche Erfassungsvorgänge:** Diese Protokolle enthalten Informationen zu erfolgreich abgeschlossenen Erfassungsvorgängen.
* **Nicht erfolgreiche Erfassungsvorgänge:** Diese Protokolle enthalten ausführliche Informationen zu fehlgeschlagenen Erfassungsvorgängen, einschließlich Fehlerdetails. 
* **Batchvorgänge für die Datenerfassung:** Diese Protokolle enthalten ausführliche Statistiken der Batches, die für die Erfassung bereit sind (Dauer, Batchgröße und Blobanzahl).

# <a name="commands-and-queries"></a>[Befehle und Abfragen](#tab/commands-and-queries)

* **Befehle:** Diese Protokolle enthalten Informationen zu Administratorbefehlen, die einen Endzustand erreicht haben.
* **Abfragen:** Diese Protokolle enthalten ausführliche Informationen zu Abfragen, die einen Endzustand erreicht haben. 

    > [!NOTE]
    > Der Abfragetext ist in den Abfrageprotokolldaten nicht enthalten.
    
# <a name="tables"></a>[Tabellen](#tab/tables)

* **TableUsageStatistics**: Diese Protokolle enthalten ausführliche Informationen zur Nutzung von Befehlen und Abfragen, die einen Endzustand erreicht haben.

    > [!NOTE]
    > Der Befehls- oder Abfragetext ist in den Protokolldaten für `TableUsageStatistics` nicht enthalten.

* **TableDetails**: Diese Protokolle enthalten ausführliche Informationen zu den Tabellen des Clusters.

---

Die Daten werden dann gemäß Ihren Angaben in einem Speicherkonto archiviert, an einen Event Hub gestreamt oder an Log Analytics gesendet.

### <a name="enable-diagnostic-logs"></a>Aktivieren von Diagnoseprotokollen

Diagnoseprotokolle sind standardmäßig deaktiviert. Führen Sie die folgenden Schritte aus, um Diagnoseprotokolle zu aktivieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die zu überwachende Azure Data Explorer-Clusterressource aus.
1. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.
  
    ![Hinzufügen von Diagnoseprotokollen](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.
1. Gehen Sie im Fenster **Diagnoseeinstellungen** wie folgt vor:

    :::image type="content" source="media/using-diagnostic-logs/configure-diagnostics-settings.png" alt-text="Konfigurieren von Diagnoseeinstellungen":::

    1. Geben Sie unter **Name der Diagnoseeinstellung** einen Namen ein.
    1. Wählen Sie mindestens ein Ziel aus: Log Analytics-Arbeitsbereich, Speicherkonto oder Event Hub.
    1. Wählen Sie die zu erfassenden Protokolle aus: `SucceededIngestion`, `FailedIngestion`, `IngestionBatching`, `Command` oder `Query`, `TableUsageStatistics` oder `TableDetails`.
    1. Wählen Sie die zu sammelnden [Metriken](using-metrics.md#supported-azure-data-explorer-metrics) aus (optional).  
    1. Wählen Sie **Speichern** aus, um die neuen Diagnoseprotokolleinstellungen und Metriken zu speichern.

Die neuen Einstellungen werden in wenigen Minuten festgelegt. Die Protokolle werden dann im konfigurierten Archivierungsziel (Speicherkonto, Event Hub oder Log Analytics) angezeigt. 

> [!NOTE]
> Wenn Sie Protokolle an Log Analytics senden, werden die Protokolle `SucceededIngestion`, `FailedIngestion`, `IngestionBatching`, `Command`, `Query`, `TableUsageStatistics` und `TableDetails` in Log Analytics-Tabellen mit den folgenden Namen gespeichert: `SucceededIngestion`, `FailedIngestion`, `ADXIngestionBatching`, `ADXCommand`, `ADXQuery`, `ADXTableUsageStatistics` bzw. `ADXTableDetails`.

## <a name="diagnostic-logs-schema"></a>Schema „Diagnoseprotokolle“

Alle [Azure Monitor-Diagnoseprotokolle verfügen über das gleiche Schema der obersten Ebene](/azure/azure-monitor/platform/diagnostic-logs-schema). Die Ereignisse von Azure Data Explorer weisen besondere Eigenschaften auf. Alle Protokolle werden im JSON-Format gespeichert.

# <a name="ingestion"></a>[Erfassung](#tab/ingestion)

### <a name="ingestion-logs-schema"></a>Erfassungsprotokollschema

JSON-Zeichenfolgen im Protokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

|Name               |BESCHREIBUNG
|---                |---
|time               |Die Zeit des Berichts
|resourceId         |Azure Resource Manager-Ressourcen-ID
|operationName      |Name des Vorgangs: MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION
|operationVersion   |Schemaversion: 1.0 
|category           |Die Kategorie des Vorgangs. `SucceededIngestion`, `FailedIngestion` oder `IngestionBatching`. Die Eigenschaften unterscheiden sich je nach [erfolgreichem Vorgang](#successful-ingestion-operation-log), [fehlgeschlagenem Vorgang](#failed-ingestion-operation-log) oder [Batchvorgang](#ingestion-batching-operation-log).
|properties         |Ausführliche Informationen zu dem Vorgang

#### <a name="successful-ingestion-operation-log"></a>Protokoll eines erfolgreichen Erfassungsvorgangs

**Beispiel:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "SucceededOn": "2019-05-27 07:55:05.3693628",
        "OperationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "Database": "Samples",
        "Table": "StormEvents",
        "IngestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "IngestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Eigenschaften eines Diagnoseprotokolls für einen erfolgreichen Vorgang**

|Name               |Beschreibung
|---                |---
|SucceededOn        |Der Abschlusszeitpunkt der Erfassung
|OperationId        |Die ID des Azure Data Explorer-Erfassungsvorgangs
|Datenbank           |Der Name der Zieldatenbank
|Tabelle              |Der Name der Zieltabelle
|IngestionSourceId  |Die ID der Erfassungsdatenquelle
|IngestionSourcePath|Der Pfad der Erfassungsdatenquelle oder der Blob-URI
|RootActivityId     |Aktivitäts-ID

#### <a name="failed-ingestion-operation-log"></a>Protokoll eines fehlgeschlagenen Erfassungsvorgangs

**Beispiel:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Eigenschaften eines Diagnoseprotokolls für einen fehlgeschlagenen Vorgang**

|Name               |Beschreibung
|---                |---
|FailedOn           |Der Abschlusszeitpunkt der Erfassung
|OperationId        |Die ID des Azure Data Explorer-Erfassungsvorgangs
|Datenbank           |Der Name der Zieldatenbank
|Tabelle              |Der Name der Zieltabelle
|IngestionSourceId  |Die ID der Erfassungsdatenquelle
|IngestionSourcePath|Der Pfad der Erfassungsdatenquelle oder der Blob-URI
|RootActivityId     |Aktivitäts-ID
|Details            |Die ausführliche Beschreibung des Fehlers und der Fehlermeldung
|ErrorCode          |Fehlercode 
|FailureStatus      |`Permanent` oder `Transient` Bei einem vorübergehenden Fehler ist das Wiederholen des Vorgangs möglicherweise erfolgreich.
|OriginatesFromUpdatePolicy|„true“, wenn die Herkunft des Fehlers eine Aktualisierungsrichtlinie ist
|ShouldRetry        |„true“, wenn die Wiederholung erfolgreich ausgeführt werden kann

#### <a name="ingestion-batching-operation-log"></a>Protokoll zu Batchvorgängen für die Datenerfassung

**Beispiel:**

```json
{
  "resourceId": "/SUBSCRIPTIONS/12534EB3-8109-4D84-83AD-576C0D5E1D06/RESOURCEGROUPS/KEREN/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KERENEUS",
  "time": "2020-05-27T07:55:05.3693628Z",
  "operationVersion": "1.0",
  "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGESTIONBATCHING/ACTION",
  "category": "IngestionBatching",
  "correlationId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735",
  "properties": {
    "Database": "Samples",
    "Table": "StormEvents",
    "BatchingType": "Size",
    "SourceCreationTime": "2020-05-27 07:52:04.9623640",
    "BatchTimeSeconds": 215.5,
    "BatchSizeBytes": 2356425,
    "DataSourcesInBatch": 4,
    "RootActivityId": "2bb51038-c7dc-4ebd-9d7f-b34ece4cb735"
  }
}

```
**Eigenschaften eines Diagnoseprotokolls zu Batchvorgängen für die Datenerfassung**

|Name               |BESCHREIBUNG
|---                   |---
| TimeGenerated        | Uhrzeit (UTC), zu der dieses Ereignis generiert wurde |
| Datenbank             | Name der Datenbank mit der Zieltabelle |
| Tabelle                | Name der Zieltabelle, in der die Daten erfasst werden |
| BatchingType         | Batchverarbeitungstyp: Gibt an, ob die Batchverarbeitungszeit oder der Grenzwert für Datengröße/Dateianzahl in der Batchrichtlinie erreicht wurde. |
| SourceCreationTime   | Frühester Zeitpunkt (UTC), zu dem Blobs in diesem Batch erstellt wurden |
| BatchTimeSeconds     | Gesamtzeit für die Batchverarbeitung dieses Batches (Sekunden) |
| BatchSizeBytes       | Gesamtgröße der nicht komprimierten Daten in diesem Batch (Bytes) |
| DataSourcesInBatch   | Anzahl von Datenquellen in diesem Batch |
| RootActivityId       | Aktivitäts-ID des Vorgangs |


# <a name="commands-and-queries"></a>[Befehle und Abfragen](#tab/commands-and-queries)

### <a name="commands-and-queries-logs-schema"></a>Schema für Befehls- und Abfrageprotokolle

JSON-Zeichenfolgen im Protokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

|Name               |BESCHREIBUNG
|---                |---
|time               |Die Zeit des Berichts
|resourceId         |Azure Resource Manager-Ressourcen-ID
|operationName      |Name des Vorgangs: „MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION“ oder „MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION“. Die Eigenschaften für Befehls- und Abfrageprotokolle unterscheiden sich jeweils.
|operationVersion   |Schemaversion: 1.0 
|category           |Kategorie des Vorgangs: `Command` oder `Query`. Die Eigenschaften für Befehls- und Abfrageprotokolle unterscheiden sich jeweils.
|properties         |Ausführliche Informationen zu dem Vorgang

#### <a name="command-log"></a>Befehlsprotokoll

**Beispiel:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION",
    "operationVersion": "1.0",
    "category": "Command",
    "properties":
    {
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d",
        "StartedOn": "2020-09-12T18:06:04.6898353Z",
        "LastUpdatedOn": "2020-09-12T18:06:04.6898353Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "XXX",
        "TotalCpu": "00:01:30.1562500",
        "CommandType": "ExtentsDrop",
        "Application": "Kusto.WinSvc.DM.Svc",
        "ResourceUtilization": "{\"CacheStatistics\":{\"Memory\":{\"Hits\":0,\"Misses\":0},\"Disk\":{\"Hits\":0,\"Misses\":0},\"Shards\":{\"Hot\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"Cold\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"BypassBytes\":0}},\"TotalCpu\":\"00:00:00\",\"MemoryPeak\":0,\"ScannedExtentsStatistics\":{\"MinDataScannedTime\":null,\"MaxDataScannedTime\":null,\"TotalExtentsCount\":0,\"ScannedExtentsCount\":0,\"TotalRowsCount\":0,\"ScannedRowsCount\":0}}",
        "Duration": "00:03:30.1562500",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a3b4136b53;5c443533-c927-4410-a5d6-4d6a5443b64f"
    }
}
```
**Eigenschaften eines Befehlsdiagnoseprotokolls**

|name               |BESCHREIBUNG
|---                |---
|RootActivityId |Die ID der Stammaktivität
|StartedOn        |Die Startzeit (UTC) dieses Befehls
|LastUpdatedOn        |Die Endzeit (UTC) dieses Befehls
|Datenbank          |Der Name der Datenbank, für die der Befehl ausgeführt wurde
|State              |Der Zustand, mit dem der Befehl beendet wurde
|FailureReason  |Die Fehlerursache
|TotalCpu |Die CPU-Gesamtdauer
|CommandType     |Befehlstyp
|Application     |Der Name der Anwendung, durch die der Befehl aufgerufen wurde
|ResourceUtilization     |Die Ressourcenverwendung des Befehls
|Duration     |Die Dauer des Befehls
|Benutzer     |Der Benutzer, der die Abfrage aufgerufen hat
|Prinzipal     |Der Prinzipal, der die Abfrage aufgerufen hat

#### <a name="query-log"></a>Abfrageprotokoll

**Beispiel:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION",
    "operationVersion": "1.0",
    "category": "Query",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-09-04T13:45:45.331925Z",
        "LastUpdatedOn": "2020-09-04T13:45:45.3484372Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "[none]",
        "TotalCpu": "00:00:00",
        "ApplicationName": "MyApp",
        "MemoryPeak": 0,
        "Duration": "00:00:00.0165122",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf",
        "ScannedExtentsStatistics": {
            "MinDataScannedTime": "2020-07-27T08:34:35.3299941",
            "MaxDataScannedTime": "2020-07-27T08:34:41.991661",
            "TotalExtentsCount": 64,
            "ScannedExtentsCount": 64,
            "TotalRowsCount": 320,
            "ScannedRowsCount": 320
        },
        "CacheStatistics": {
            "Memory": {
                "Hits": 192,
                "Misses": 0
          },
            "Disk": {
                "Hits": 0,
                "Misses": 0
      },
            "Shards": {
                "Hot": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
                "Cold": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
            "BypassBytes": 0
      }
    },
    "ResultSetStatistics": {
        "TableCount": 1,
        "TablesStatistics": [
        {
          "RowCount": 1,
          "TableSize": 9
        }
      ]
    }
  }
}
```

**Eigenschaften eines Abfragediagnoseprotokolls**

|name               |BESCHREIBUNG
|---                |---
|RootActivityId |Die ID der Stammaktivität
|StartedOn        |Die Startzeit (UTC) dieses Befehls
|LastUpdatedOn           |Die Endzeit (UTC) dieses Befehls
|Datenbank              |Der Name der Datenbank, für die der Befehl ausgeführt wurde
|State  |Der Zustand, mit dem der Befehl beendet wurde
|FailureReason|Die Fehlerursache
|TotalCpu     |Die CPU-Gesamtdauer
|ApplicationName            |Der Name der Anwendung, von der die Abfrage aufgerufen wurde
|MemoryPeak          |Die maximale Arbeitsspeicherauslastung
|Duration      |Die Dauer des Befehls
|Benutzer|Der Benutzer, der die Abfrage aufgerufen hat
|Prinzipal        |Der Prinzipal, der die Abfrage aufgerufen hat
|ScannedExtentsStatistics        | Enthält Statistiken zu überprüften Erweiterungen
|MinDataScannedTime        |Die minimale Datenüberprüfungszeit
|MaxDataScannedTime        |Die maximale Datenüberprüfungszeit
|TotalExtentsCount        |Die Gesamtanzahl von Erweiterungen
|ScannedExtentsCount        |Die Anzahl überprüfter Erweiterungen
|TotalRowsCount        |Die Gesamtanzahl von Zeilen
|ScannedRowsCount        |Die Gesamtanzahl überprüfter Zeilen
|CacheStatistics        |Enthält Cachestatistiken
|Arbeitsspeicher        |Enthält CPU-Cachestatistiken
|Treffer        |Arbeitsspeicher-Cachetreffer
|Cachefehler        |Arbeitsspeicher-Cachefehler
|Datenträger        |Enthält Cachedatenträgerstatistiken
|Treffer        |Datenträgercachetreffer
|Cachefehler        |Datenträgercachefehler
|Shards        |Enthält Cachestatistiken zu Hot Shards und Cold Shards
|heiße Ebene        |Enthält Cachestatistiken zu Hot Shards
|HitBytes        |Cachetreffer für Hot Shards
|MissBytes        |Cachefehler für Hot Shards
|RetrieveBytes        | Aus dem Cache abgerufene Bytes für Hot Shards
|Kalt        |Enthält Cachestatistiken zu Cold Shards
|HitBytes        |Cachetreffer für Cold Shards
|MissBytes        |Cachefehler für Cold Shards
|RetrieveBytes        |Aus dem Cache abgerufene Bytes für Cold Shards
|BypassBytes        |Umgangene Bytes für den Shards-Cache
|ResultSetStatistics        |Enthält Resultsetstatistiken
|TableCount        |Tabellenanzahl für Resultset
|TablesStatistics        |Enthält Resultset-Tabellenstatistiken
|RowCount        | Tabellenzeilenanzahl für Resultset
|TableSize        |Tabellenzeilenanzahl für Resultset


# <a name="tables"></a>[Tabellen](#tab/tables)

### <a name="tableusagestatistics-and-tabledetails-logs-schema"></a>Schema der Protokolle „TableUsageStatistics“ und „TableDetails“

JSON-Zeichenfolgen im Protokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

|Name               |BESCHREIBUNG
|---                |---
|time               |Die Zeit des Berichts
|resourceId         |Azure Resource Manager-Ressourcen-ID
|operationName      |Name des Vorgangs: MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ. Eigenschaften sind für TableUsageStatistics und TableDetails identisch.
|operationVersion   |Schemaversion: 1.0 
|properties         |Ausführliche Informationen zu dem Vorgang

#### <a name="tableusagestatistics-log"></a>TableUsageStatistics-Protokoll

**Beispiel:**

```json
{
    "resourceId": "/SUBSCRIPTIONS/0571b364-eeeb-4f28-ba74-90a8b4132b53/RESOURCEGROUPS/MYRG/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/MYKUSTOCLUSTER",
    "time": "08-04-2020 16:42:29",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ",
    "correlationId": "MyApp.Kusto.DM.MYKUSTOCLUSTER.ShowTableUsageStatistics.e10fe80b-6f4d-4b7e-9756-b87720f88901",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-08-19T11:51:41.1258308Z",
        "Database": "MyDB",
        "Table": "MyTable",
        "MinCreatedOn": "2020-07-20T09:16:00.9906347Z",
        "MaxCreatedOn": "2020-08-19T11:50:37.1233374Z",
        "Application": "MyApp",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf"
    }
}
```

**Eigenschaften eines TableUsageStatistics-Diagnoseprotokolls**

|Name               |BESCHREIBUNG
|---                |---
|RootActivityId |Die ID der Stammaktivität
|StartedOn        |Die Startzeit (UTC) dieses Befehls
|Datenbank          |den Namen der Datenbank
|TableName              |Der Name der Tabelle.
|MinCreatedOn  |Zeit der ältesten Erweiterungen der Tabelle
|MaxCreatedOn |Zeit der letzten Erweiterungen der Tabelle
|ApplicationName     |Der Name der Anwendung, durch die der Befehl aufgerufen wurde
|Benutzer     |Der Benutzer, der die Abfrage aufgerufen hat
|Prinzipal     |Der Prinzipal, der die Abfrage aufgerufen hat

#### <a name="tabledetails-log"></a>TableDetails-Protokoll

**Beispiel:**

```json
{
    "resourceId": "/SUBSCRIPTIONS/0571b364-eeeb-4f28-ba74-90a8b4132b53/RESOURCEGROUPS/MYRG/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/MYKUSTOCLUSTER",
    "time": "08-04-2020 16:42:29",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/DATABASE/SCHEMA/READ",
    "correlationId": "MyApp.Kusto.DM.MYKUSTOCLUSTER.ShowTableUsageStatistics.e10fe80b-6f4d-4b7e-9756-b87720f88901",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "TableName": "MyTable",
        "DatabaseName": "MyDB",
        "TotalExtentSize": 9632.0,
        "TotalOriginalSize": 4143.0,
        "HotExtentSize": 0.0,
        "RetentionPolicyOrigin": "table",
        "RetentionPolicy": "{\"SoftDeletePeriod\":\"90.00:00:00\",\"Recoverability\":\"Disabled\"}",
        "CachingPolicyOrigin": "database",
        "CachingPolicy": "{\"DataHotSpan\":\"7.00:00:00\",\"IndexHotSpan\":\"7.00:00:00\",\"ColumnOverrides\":[]}",
        "MaxExtentsCreationTime": "2020-08-30T02:44:43.9824696Z",
        "MinExtentsCreationTime": "2020-08-30T02:38:42.3031288Z",
        "TotalExtentCount": 1164,
        "TotalRowCount": 223325,
        "HotExtentCount": 29,
        "HotOriginalSize": 1388213,
        "HotRowCount": 5117
  }
}
```

**Eigenschaften eines TableDetails-Diagnoseprotokolls**

|Name               |BESCHREIBUNG
|---                |---
|RootActivityId |Die ID der Stammaktivität
|TableName        |Der Name der Tabelle.
|DatabaseName           |Der Name der Datenbank
|TotalExtentSize              |Die gesamte ursprüngliche Größe der Daten in der Tabelle (in Bytes)
|HotExtentSize  |Die gesamte ursprüngliche Größe (in Bytes) der Erweiterungen (komprimierte Größe und Indexgröße) in der Tabelle (gespeichert im Cache für heiße Daten)
|RetentionPolicyOrigin |Ursprungsentität der Aufbewahrungsrichtlinie (Tabelle/Datenbank/Cluster)
|RetentionPolicy     |Die effektive Entitätsaufbewahrungsrichtlinie der Tabelle (als JSON serialisiert)
|CachingPolicyOrigin            |Ursprungsentität der Cacherichtlinie (Tabelle/Datenbank/Cluster)
|CachingPolicy          |Die effektive Entitätscacherichtlinie der Tabelle (als JSON serialisiert)
|MaxExtentsCreationTime      |Die maximale Erstellungszeit einer Erweiterung in der Tabelle (oder NULL, wenn keine Erweiterungen vorhanden sind)
|MinExtentsCreationTime |Die minimale Erstellungszeit einer Erweiterung in der Tabelle (oder NULL, wenn keine Erweiterungen vorhanden sind)
|TotalExtentCount        |Gesamtanzahl der Erweiterungen in der Tabelle
|TotalRowCount        |Gesamtanzahl der Zeilen in der Tabelle
|MinDataScannedTime        |Die minimale Datenüberprüfungszeit
|MaxDataScannedTime        |Die maximale Datenüberprüfungszeit
|TotalExtentsCount        |Die Gesamtanzahl von Erweiterungen
|ScannedExtentsCount        |Die Anzahl überprüfter Erweiterungen
|TotalRowsCount        |Die Gesamtanzahl von Zeilen
|HotExtentCount        |Gesamtanzahl der Erweiterungen in der Tabelle (gespeichert im Cache für heiße Daten)
|HotOriginalSize        |Die gesamte ursprüngliche Größe (in Bytes) der Daten in der Tabelle (gespeichert im Cache für heiße Daten)
|HotRowCount        |Gesamtanzahl der Zeilen in der Tabelle (gespeichert im Cache für heiße Daten)

---

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Metriken zum Überwachen der Clusterintegrität](using-metrics.md)
* [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](ingest-data-no-code.md) für Erfassungsdiagnoseprotokolle
