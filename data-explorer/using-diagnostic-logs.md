---
title: Überwachen von Azure Data Explorer-Erfassungsvorgängen mithilfe von Diagnoseprotokollen
description: Erfahren Sie, wie Sie Diagnoseprotokolle für Azure Data Explorer einrichten, um Erfassungsvorgänge zu überwachen.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: c4c7114dd53b8f77150f88cf3da5d26ca73bb93b
ms.sourcegitcommit: 2baa6a1c25ba25ef02a53b4f8d023ea821bd04ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81527587"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs"></a>Überwachen von Azure Data Explorer-Erfassungsvorgängen mithilfe von Diagnoseprotokollen

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Tabelle in einer Datenbank, damit Sie sie abfragen können. [Azure Monitor-Aktivitätsprotokolle](/azure/azure-monitor/platform/diagnostic-logs-overview) enthalten Daten zum Betrieb von Azure-Ressourcen. In Azure Data Explorer werden Diagnoseprotokolle verwendet, um Erkenntnisse zu erfolgreichen und fehlgeschlagenen Erfassungsvorgängen zu gewinnen. Sie können Vorgangsprotokolle in Azure Storage, Event Hub oder Log Analytics exportieren, um den Erfassungsstatus zu überwachen. Protokolle aus Azure Storage und Azure Event Hub können zur weiteren Analyse an eine Tabelle im Azure Data Explorer-Cluster weitergeleitet werden.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/), falls Sie nicht über ein Azure-Abonnement verfügen.
* Erstellen Sie einen [Cluster und eine Datenbank](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Einrichten von Diagnoseprotokollen für einen Azure Data Explorer-Cluster

Mit Diagnoseprotokollen kann die Sammlung der folgenden Protokolldaten konfiguriert werden:
* Erfolgreiche Erfassungsvorgänge: Diese Protokolle enthalten Informationen zu erfolgreich abgeschlossenen Erfassungsvorgängen.
* Fehlgeschlagene Erfassungsvorgänge: Diese Protokolle enthalten ausführliche Informationen zu fehlgeschlagenen Erfassungsvorgängen, einschließlich Fehlerdetails. 

Die Daten werden dann gemäß Ihren Angaben in einem Speicherkonto archiviert, an einen Event Hub gestreamt oder an Log Analytics gesendet.

### <a name="enable-diagnostic-logs"></a>Aktivieren von Diagnoseprotokollen

Diagnoseprotokolle sind standardmäßig deaktiviert. Führen Sie die folgenden Schritte aus, um Diagnoseprotokolle zu aktivieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die zu überwachende Azure Data Explorer-Clusterressource aus.
1. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.
  
    ![Hinzufügen von Diagnoseprotokollen](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.
1. Im Fenster **Diagnoseeinstellungen**:
 
    ![Konfiguration der Diagnoseeinstellungen](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Geben Sie in **Name** einen Namen für die Diagnoseeinstellung ein.
    1. Wählen Sie ein oder mehrere Ziele aus: Speicherkonto, Event Hub oder Log Analytics.
    1. Wählen Sie die zu sammelnden Protokolle aus: `SucceededIngestion` oder `FailedIngestion`.
    1. Wählen Sie die zu sammelnden [Metriken](using-metrics.md#supported-azure-data-explorer-metrics) aus (optional).  
    1. Wählen Sie **Speichern** aus, um die neuen Diagnoseprotokolleinstellungen und Metriken zu speichern.

Die neuen Einstellungen werden in wenigen Minuten festgelegt. Die Protokolle werden dann im konfigurierten Archivierungsziel (Speicherkonto, Event Hub oder Log Analytics) angezeigt. 

## <a name="diagnostic-logs-schema"></a>Schema „Diagnoseprotokolle“

Alle [Azure Monitor-Diagnoseprotokolle verfügen über das gleiche Schema der obersten Ebene](/azure/azure-monitor/platform/diagnostic-logs-schema). Die Ereignisse von Azure Data Explorer weisen besondere Eigenschaften auf. Alle Protokolle werden im JSON-Format gespeichert.

### <a name="ingestion-logs-schema"></a>Erfassungsprotokollschema

JSON-Zeichenfolgen im Protokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

|Name               |BESCHREIBUNG
|---                |---
|time               |Die Zeit des Berichts
|resourceId         |Azure Resource Manager-Ressourcen-ID
|operationName      |Name des Vorgangs: MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION
|operationVersion   |Schemaversion: 1.0 
|category           |Die Kategorie des Vorgangs. `SucceededIngestion` oder `FailedIngestion` Die Eigenschaften unterscheiden sich je nach [erfolgreichem Vorgang](#successful-ingestion-operation-log) oder [fehlgeschlagenem Vorgang](#failed-ingestion-operation-log).
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
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Eigenschaften eines Diagnoseprotokolls für einen erfolgreichen Vorgang**

|Name               |BESCHREIBUNG
|---                |---
|succeededOn        |Der Abschlusszeitpunkt der Erfassung
|operationId        |Die ID des Azure Data Explorer-Erfassungsvorgangs
|database           |Der Name der Zieldatenbank
|table              |Der Name der Zieltabelle
|ingestionSourceId  |Die ID der Erfassungsdatenquelle
|ingestionSourcePath|Der Pfad der Erfassungsdatenquelle oder der Blob-URI
|rootActivityId     |Aktivitäts-ID

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

|Name               |BESCHREIBUNG
|---                |---
|failedOn           |Der Abschlusszeitpunkt der Erfassung
|operationId        |Die ID des Azure Data Explorer-Erfassungsvorgangs
|database           |Der Name der Zieldatenbank
|table              |Der Name der Zieltabelle
|ingestionSourceId  |Die ID der Erfassungsdatenquelle
|ingestionSourcePath|Der Pfad der Erfassungsdatenquelle oder der Blob-URI
|rootActivityId     |Aktivitäts-ID
|Details            |Die ausführliche Beschreibung des Fehlers und der Fehlermeldung
|errorCode          |Fehlercode 
|failureStatus      |`Permanent` oder `Transient` Bei einem vorübergehenden Fehler ist das Wiederholen des Vorgangs möglicherweise erfolgreich.
|originatesFromUpdatePolicy|„true“, wenn die Herkunft des Fehlers eine Aktualisierungsrichtlinie ist
|shouldRetry        |„true“, wenn die Wiederholung erfolgreich ausgeführt werden kann

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer](ingest-data-no-code.md)
* [Verwenden von Metriken zum Überwachen der Clusterintegrität](using-metrics.md)

