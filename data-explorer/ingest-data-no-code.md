---
title: 'Tutorial: Erfassen von Überwachungsdaten in Azure Data Explorer ohne Code'
description: In diesem Tutorial wird beschrieben, wie Sie Überwachungsdaten in Azure Data Explorer erfassen, ohne Code verwenden zu müssen, und diese Daten dann abfragen.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: fa4c47994ac96486fe6889e162fe917a5cef3f3e
ms.sourcegitcommit: 88291fd9cebc26e5210463cb95be5540bf84eef8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437428"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Tutorial: Erfassen und Abfragen von Überwachungsdaten in Azure Data Explorer 

In diesem Tutorial wird beschrieben, wie Sie Daten aus Diagnose- und Aktivitätsprotokolldaten in einem Azure Data Explorer-Cluster erfassen, ohne jeglichen Code schreiben zu müssen. Mit dieser einfachen Erfassungsmethode können Sie schnell damit beginnen, Azure Data Explorer zu Datenanalysezwecken abzufragen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Tabellen und einer Erfassungszuordnung in einer Azure Data Explorer-Datenbank
> * Formatieren der erfassten Daten mit einer Updaterichtlinie
> * Erstellen eines [Event Hub](/azure/event-hubs/event-hubs-about) und Herstellen einer Verbindung mit Azure Data Explorer
> * Streamen von Daten an einen Event Hub aus Azure Monitor-[Diagnosemetriken und -protokollen](/azure/azure-monitor/platform/diagnostic-settings) und [Aktivitätsprotokollen](/azure/azure-monitor/platform/activity-logs-overview).
> * Abfragen der erfassten Daten mit Azure Data Explorer

> [!NOTE]
> Erstellen aller Ressourcen an demselben Azure-Standort bzw. in derselben Region. 

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* [Schnellstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md). In diesem Tutorial lautet der Datenbankname *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor-Datenanbieter: Diagnosemetriken und -protokolle und Aktivitätsprotokolle

Unten können Sie die Daten anzeigen, die von den Diagnosemetriken und -protokollen und Aktivitätsprotokollen von Azure Monitor bereitgestellt werden, und sich damit vertraut machen. Basierend auf diesen Datenschemas erstellen Sie eine Erfassungspipeline. Beachten Sie, dass jedes Ereignis in einem Protokoll ein Array von Datensätzen enthält. Dieses Array von Datensätzen wird später im Tutorial aufgeteilt.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Beispiele für Diagnosemetriken und -protokolle und Aktivitätsprotokolle

Azure-Diagnosemetriken und -protokolle und Aktivitätsprotokolle werden von einem Azure-Dienst ausgegeben und liefern Daten zum Betrieb dieses Diensts. 

# <a name="diagnostic-metrics"></a>[Diagnosemetriken](#tab/diagnostic-metrics)
#### <a name="example"></a>Beispiel

Diagnosemetriken werden mit einem Aggregationsintervall von einer Minute aggregiert. Hier sehen ein Beispiel für ein Azure Data Explorer-Metrikereignisschema zur Abfragedauer:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[Diagnoseprotokolle](#tab/diagnostic-logs)
#### <a name="example"></a>Beispiel

Hier sehen Sie ein Beispiel für ein [Protokoll der Erfassung von Diagnosedaten](using-diagnostic-logs.md#diagnostic-logs-schema) von Azure Data Explorer:

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[Aktivitätsprotokolle](#tab/activity-logs)
#### <a name="example"></a>Beispiel

Azure-Aktivitätsprotokolle sind Protokolle auf Abonnementebene, die einen Einblick in die Vorgänge geben, die mit Ressourcen in Ihrem Abonnement ausgeführt werden. Hier sehen Sie ein Beispiel für ein Aktivitätsprotokollereignis zur Überprüfung des Zugriffs:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Einrichten einer Erfassungspipeline in Azure Data Explorer

Das Einrichten einer Azure Data Explorer-Pipeline umfasst mehrere Schritte, z. B. die [Tabellenerstellung und Datenerfassung](ingest-sample-data.md#ingest-data). Sie können die Daten auch ändern, zuordnen und aktualisieren.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Herstellen einer Verbindung mit der Azure Data Explorer-Webbenutzeroberfläche

Wählen Sie in Azure Data Explorer für *TestDatabase* die Option **Abfrage**, um die Azure Data Explorer-Webbenutzeroberfläche zu öffnen.

![Seite „Abfrage“](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Erstellen der Zieltabellen

Die Struktur der Azure Monitor-Protokolle ist nicht tabellarisch. Sie bearbeiten die Daten und erweitern jedes Ereignis auf einen oder mehrere Datensätze. Die Rohdaten werden in eine Zwischentabelle namens *ActivityLogsRawRecords* für Aktivitätsprotokolle und *DiagnosticRawRecords* für Diagnosemetriken und -protokolle aufgenommen. Die Daten werden nun bearbeitet und erweitert. Mithilfe einer Updaterichtlinie werden die erweiterten Daten dann in die Tabelle *ActivityLogs* für Aktivitätsprotokolle, *DiagnosticMetrics* für Diagnosemetriken und *DiagnosticLogs* für Diagnoseprotokolle aufgenommen. Das bedeutet, dass Sie zwei separate Tabellen für das Erfassen von Aktivitätsprotokollen und drei separate Tabellen für das Erfassen von Diagnosemetriken und -protokollen erstellen müssen.

Verwenden Sie die Azure Data Explorer-Webbenutzeroberfläche, um die Zieltabellen in der Azure Data Explorer-Datenbank zu erstellen.

# <a name="diagnostic-metrics"></a>[Diagnosemetriken](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Erstellen von Tabellen für die Diagnosemetriken

1. Erstellen Sie in der Datenbank *TestDatabase* eine Tabelle mit dem Namen *DiagnosticMetrics*, um die Diagnosemetrik-Datensätze zu speichern. Verwenden Sie den folgenden `.create table`-Steuerungsbefehl:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Wählen Sie **Ausführen**, um die Tabelle zu erstellen.

    ![Abfrage ausführen](media/ingest-data-no-code/run-query.png)

1. Erstellen Sie die Zwischendatentabelle *DiagnosticRawRecords* in der *TestDatabase*-Datenbank für die Datenbearbeitung mit der folgenden Abfrage. Wählen Sie **Ausführen**, um die Tabelle zu erstellen.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Legen Sie für die Zwischentabelle die NULL-[Beibehaltungsrichtlinie](kusto/management/retention-policy.md) fest:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Diagnoseprotokolle](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Erstellen von Tabellen für die Diagnoseprotokolle 

1. Erstellen Sie in der Datenbank *TestDatabase* eine Tabelle mit dem Namen *DiagnosticLogs*, um die Diagnoseprotokoll-Datensätze zu speichern. Verwenden Sie den folgenden `.create table`-Steuerungsbefehl:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Wählen Sie **Ausführen**, um die Tabelle zu erstellen.

1. Erstellen Sie die Zwischendatentabelle *DiagnosticRawRecords* in der *TestDatabase*-Datenbank für die Datenbearbeitung mit der folgenden Abfrage. Wählen Sie **Ausführen**, um die Tabelle zu erstellen.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Legen Sie für die Zwischentabelle die NULL-[Beibehaltungsrichtlinie](kusto/management/retention-policy.md) fest:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Aktivitätsprotokolle](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Erstellen von Tabellen für die Aktivitätsprotokolle 

1. Erstellen Sie eine Tabelle mit dem Namen *ActivityLogs* in der Datenbank *TestDatabase*, um Aktivitätsprotokoll-Datensätze zu erhalten. Führen Sie zum Erstellen der Tabelle die folgende Azure Data Explorer-Abfrage aus:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Erstellen Sie die Zwischentabelle mit dem Namen *ActivityLogsRawRecords* für Daten in der Datenbank *TestDatabase*, um die Bearbeitung der Daten zu ermöglichen:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Legen Sie für die Zwischentabelle die NULL-[Beibehaltungsrichtlinie](kusto/management/retention-policy.md) fest:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Erstellen von Tabellenzuordnungen

 Da das Datenformat `json` lautet, ist eine Datenzuordnung erforderlich. Mit der `json`-Zuordnung wird jeder JSON-Pfad einem Tabellenspaltennamen zugeordnet.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosemetriken/Diagnoseprotokolle](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Zuordnen von Diagnosemetriken und -protokollen zur Tabelle

Verwenden Sie die folgende Abfrage, um die Daten der Diagnosemetrik- und -protokolldaten der Tabelle zuzuordnen:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","Properties":{"path":"$.records"}}]'
```

# <a name="activity-logs"></a>[Aktivitätsprotokolle](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Zuordnen von Aktivitätsprotokollen zur Tabelle

Verwenden Sie die folgende Abfrage, um die Daten der Aktivitätsprotokolle der Tabelle zuzuordnen:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","Properties":{"path":"$.records"}}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Erstellen der Updaterichtlinie für Metrik- und Protokolldaten

# <a name="diagnostic-metrics"></a>[Diagnosemetriken](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Erstellen einer Datenaktualisierungsrichtlinie für Diagnosemetriken

1. Erstellen Sie eine [Funktion](kusto/management/functions.md), mit der die Sammlung an Diagnosemetrikdatensätzen so erweitert wird, dass jeder Wert der Sammlung in einer separaten Zeile angeordnet wird. Verwenden Sie den Operator [`mv-expand`](kusto/query/mvexpandoperator.md):
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Fügen Sie die [Updaterichtlinie](kusto/management/updatepolicy.md) der Zieltabelle hinzu. Mit dieser Richtlinie wird die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle *DiagnosticRawRecords* ausgeführt, und die Ergebnisse werden in der Tabelle *DiagnosticMetrics* erfasst:

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Diagnoseprotokolle](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Erstellen einer Datenaktualisierungsrichtlinie für Diagnoseprotokolle

1. Erstellen Sie eine [Funktion](kusto/management/functions.md), mit der die Sammlung an Diagnoseprotokolldatensätzen so erweitert wird, dass jeder Wert der Sammlung in einer separaten Zeile angeordnet wird. Sie aktivieren Datenerfassungsprotokolle in einem Azure Data Explorer-Cluster und verwenden das [Erfassungsprotokollschema](using-diagnostic-logs.md#diagnostic-logs-schema). Sie erstellen eine Tabelle für erfolgreiche und für fehlgeschlagene Datenerfassung. Für die erfolgreiche Erfassung bleiben einige der Felder leer (z. B. ErrorCode). Verwenden Sie den Operator [`mv-expand`](kusto/query/mvexpandoperator.md):

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Fügen Sie die [Updaterichtlinie](kusto/management/updatepolicy.md) der Zieltabelle hinzu. Mit dieser Richtlinie wird die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle *DiagnosticRawRecords* ausgeführt, und die Ergebnisse werden in der Tabelle *DiagnosticLogs* erfasst:

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Aktivitätsprotokolle](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Erstellen einer Datenaktualisierungsrichtlinie für Aktivitätsprotokolle

1. Erstellen Sie eine [Funktion](kusto/management/functions.md), mit der die Sammlung an Aktivitätsprotokolldatensätzen so erweitert wird, dass jeder Wert der Sammlung in einer separaten Zeile angeordnet wird. Verwenden Sie den Operator [`mv-expand`](kusto/query/mvexpandoperator.md):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Fügen Sie die [Updaterichtlinie](kusto/management/updatepolicy.md) der Zieltabelle hinzu. Mit dieser Richtlinie wird die Abfrage automatisch für alle neu erfassten Daten in der Zwischentabelle *ActivityLogsRawRecords* ausgeführt, und die Ergebnisse werden in der Tabelle *ActivityLogs* erfasst:

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Erstellen eines Azure Event Hubs-Namespace

Mit Azure-Diagnoseeinstellungen können Metriken und Protokolle in ein Speicherkonto oder einen Event Hub exportiert werden. In diesem Tutorial leiten wir die Metriken und Protokolle über einen Event Hub weiter. Sie erstellen mit den folgenden Schritten einen Event Hubs-Namespace und einen Event Hub für die Diagnosemetriken und -protokolle. Azure Monitor erstellt den Event Hub *insights-operational-logs* für die Aktivitätsprotokolle.

1. Erstellen Sie einen Event Hub, indem Sie im Azure-Portal eine Azure Resource Manager-Vorlage verwenden. Klicken Sie zum Ausführen der restlichen Schritte dieses Artikels mit der rechten Maustaste auf die Schaltfläche **In Azure bereitstellen**, und wählen Sie die Option **In neuem Fenster öffnen**. Mit der Schaltfläche **In Azure bereitstellen** gelangen Sie zum Azure-Portal.

    [![Schaltfläche „In Azure bereitstellen“](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Erstellen Sie einen Event Hubs-Namespace und einen Event Hub für die Diagnoseprotokolle.

    ![Event Hub-Erstellung](media/ingest-data-no-code/event-hub.png)

1. Füllen Sie das Formular mit den folgenden Informationen aus. Verwenden Sie für alle Einstellungen, die nicht in der folgenden Tabelle aufgeführt sind, die jeweiligen Standardwerte.

    **Einstellung** | **Empfohlener Wert** | **Beschreibung**
    |---|---|---|
    | **Abonnement** | *Ihr Abonnement* | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Event Hub verwenden möchten.|
    | **Ressourcengruppe** | *test-resource-group* | Erstellen Sie eine neue Ressourcengruppe. |
    | **Location** | Wählen Sie die Region aus, die Ihre Anforderungen am besten erfüllt. | Erstellen Sie den Event Hubs-Namespace an demselben Standort wie die anderen Ressourcen.
    | **Namespacename** | *AzureMonitoringData* | Wählen Sie einen eindeutigen Namen, der Ihren Namespace identifiziert.
    | **Event Hub-Name** | *DiagnosticData* | Der Event Hub befindet sich unter dem Namespace, der einen eindeutigen Bereichscontainer bereitstellt. |
    | **Name der Consumergruppe** | *adxpipeline* | Erstellen Sie einen Consumergruppennamen. Durch Consumergruppen können mehrere verarbeitende Anwendungen jeweils über eine separate Ansicht des Ereignisdatenstroms verfügen. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Verbinden von Azure Monitor-Metriken und -Protokollen mit Ihrem Event Hub

Jetzt müssen Sie für Ihre Diagnosemetriken und -protokolle und Aktivitätsprotokolle eine Verbindung mit dem Event Hub herstellen.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosemetriken/Diagnoseprotokolle](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Verbinden von Diagnosemetriken und Protokollen mit Ihrem Event Hub

Wählen Sie eine Ressource aus, für die Metriken exportiert werden sollen. Mehrere Ressourcentypen unterstützen Diagnosedaten, z. B. Event Hubs-Namespace, Azure Key Vault, Azure IoT Hub und Azure Data Explorer-Cluster. In diesem Tutorial verwenden wir einen Azure Data Explorer-Cluster als Ressource. Wir überprüfen die Protokolle für Abfrageleistungsmetriken und Erfassungsergebnisse.

1. Wählen Sie im Azure-Portal Ihren Kusto-Cluster aus.
1. Wählen Sie **Diagnoseeinstellungen** und dann den Link **Diagnose aktivieren**. 

    ![Diagnoseeinstellungen](media/ingest-data-no-code/diagnostic-settings.png)

1. Der Bereich **Diagnoseeinstellungen** wird geöffnet. Führen Sie die folgenden Schritte aus:
   1. Geben Sie den Diagnoseprotokolldaten den Namen *ADXExportedData*.
   1. Aktivieren Sie unter **LOG** die Kontrollkästchen **SucceededIngestion** und **FailedIngestion**.
   1. Aktivieren Sie unter **METRIC** das Kontrollkästchen **Abfrageleistung**.
   1. Aktivieren Sie das Kontrollkästchen **An einen Event Hub streamen**.
   1. Wählen Sie **Konfigurieren** aus.

      ![Bereich „Diagnoseeinstellungen“](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Konfigurieren Sie im Bereich **Event Hub auswählen**, wie Daten aus Diagnoseprotokollen auf den von Ihnen erstellten Event Hub exportiert werden sollen:
    1. Wählen Sie in der Liste **Event Hub-Namespace auswählen** die Option *AzureMonitoringData*.
    1. Wählen Sie in der Liste **Event Hub-Name auswählen** die Option *DiagnosticData*.
    1. Wählen Sie in der Liste **Event Hub-Richtlinienname auswählen**  die Option **RootManagerSharedAccessKey**.
    1. Klicken Sie auf **OK**.

1. Wählen Sie **Speichern** aus.

# <a name="activity-logs"></a>[Aktivitätsprotokolle](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Verbinden von Aktivitätsprotokollen mit Ihrem Event Hub

1. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Aktivitätsprotokoll**.
1. Das Fenster **Aktivitätsprotokoll** wird geöffnet. Wählen Sie **Diagnoseeinstellungen** aus.

    ![Fenster „Aktivitätsprotokoll“](media/ingest-data-no-code/activity-log.png)

1. Das Fenster **Diagnoseeinstellungen** wird geöffnet. Wählen Sie **+Diagnoseeinstellung hinzufügen**  aus.

    :::image type="content" source="media/ingest-data-no-code/add-diagnosting-setting.png" alt-text="Hinzufügen von Diagnoseeinstellungen im Fenster „Diagnoseeinstellungen“, Azure Data Explorer-Portal":::

1. Ein neues Fenster **Diagnoseeinstellungen** wird geöffnet. 

    :::image type="content" source="media/ingest-data-no-code/export-activity-log.PNG" alt-text="Fenster „Diagnoseeinstellungen“ mit Feldern zum Ausfüllen: Azure Data Explorer-Portal":::

    Führen Sie die folgenden Schritte aus:
    1. Geben Sie im Feld **Name der Diagnoseeinstellung** einen Namen ein.  
    1. Wählen Sie auf der linken Seite der Kontrollkästchen die Plattformprotokolle aus, die Sie von einem Abonnement erfassen möchten.
    1. Aktivieren Sie das Kontrollkästchen **An einen Event Hub streamen**.
    1. Wählen Sie Ihr Abonnement aus.
    1. Wählen Sie in der Liste **Event Hub-Namespace** die Option *AzureMonitoringData* aus.
    1. Wählen Sie optional den **Event Hub-Namen** aus.
    1. Wählen Sie in der Liste **Event Hub-Richtlinienname** den Event Hub-Standardrichtliniennamen aus.
    1. Wählen Sie oben links im Fenster die Option **Speichern**. Ein Event Hub mit dem Namen *insights-operational-logs* wird erstellt (es sei denn, Sie haben oben einen Event Hub-Namen ausgewählt).
      
    

---

### <a name="see-data-flowing-to-your-event-hubs"></a>Verfolgen des Datenflusses zu Ihren Event Hubs

1. Warten Sie einige Minuten, bis die Verbindung definiert und der Export des Aktivitätsprotokolls auf den Event Hub abgeschlossen wurde. Navigieren Sie zu Ihrem Event Hubs-Namespace, um die von Ihnen erstellten Event Hubs anzuzeigen.

    ![Erstellte Event Hubs](media/ingest-data-no-code/event-hubs-created.png)

1. Verfolgen Sie den Datenfluss zu Ihrem Event Hub:

    ![Event Hub-Daten](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Herstellen einer Verbindung für einen Event Hub mit Azure Data Explorer

Nun müssen Sie die Datenverbindungen für Ihre Diagnosemetriken und -protokolle und Aktivitätsprotokolle herstellen.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Erstellen der Datenverbindung für Diagnosemetriken und -protokolle und Aktivitätsprotokolle

1. Wählen Sie in Ihrem Azure Data Explorer-Cluster mit dem Namen *kustodocs* im Menü auf der linken Seite die Option **Datenbanken**.
1. Wählen Sie im Fenster **Datenbanken** Ihre Datenbank *TestDatabase* aus.
1. Wählen Sie im Menü auf der linken Seite die Option **Datenerfassung**.
1. Klicken Sie im Fenster **Datenerfassung** auf **+ Datenverbindung hinzufügen**.
1. Geben Sie im Fenster **Datenverbindung** die folgenden Informationen ein:

    ![Event Hub-Datenverbindung](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnosemetriken/Diagnoseprotokolle](#tab/diagnostic-metrics+diagnostic-logs) 

1. Verwenden Sie im Fenster **Datenverbindung** die folgenden Einstellungen:

    Datenquelle:

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Name der Datenverbindung** | *DiagnosticsLogsConnection* | Der Name der Verbindung, die Sie im Azure-Daten-Explorer erstellen möchten.|
    | **Event Hub-Namespace** | *AzureMonitoringData* | Der von Ihnen zuvor ausgewählte Name, der Ihren Namespace identifiziert. |
    | **Event Hub** | *DiagnosticData* | Der von Ihnen erstellte Event Hub. |
    | **Consumergruppe** | *adxpipeline* | Die Consumergruppe, die in dem von Ihnen erstellten Event Hub definiert ist. |
    | | |

    Zieltabelle:

    Es stehen zwei Routingoptionen zur Verfügung: *statisch* und *dynamisch*. In diesem Tutorial verwenden Sie statisches Routing (Standardeinstellung), für das Sie den Tabellennamen, das Datenformat und die Zuordnung angeben. Lassen Sie das Kontrollkästchen **My data includes routing info** (Meine Daten enthalten Routinginformationen) deaktiviert.

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Tabelle** | *DiagnosticRawRecords* | Die Tabelle, die Sie in der Datenbank *TestDatabase* erstellt haben. |
    | **Datenformat** | *JSON* | Das Format, das in der Tabelle verwendet wird. |
    | **Spaltenzuordnung** | *DiagnosticRawRecordsMapping* | Die Zuordnung, die Sie in der Datenbank *TestDatabase* erstellt haben und mit der eingehende JSON-Daten den Spaltennamen und Datentypen der Tabelle *DiagnosticRawRecords* zugeordnet werden.|
    | | |

1. Wählen Sie **Erstellen** aus.  

# <a name="activity-logs"></a>[Aktivitätsprotokolle](#tab/activity-logs)

1. Verwenden Sie im Fenster **Datenverbindung** die folgenden Einstellungen:

    Datenquelle:

    **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Name der Datenverbindung** | *ActivityLogsConnection* | Der Name der Verbindung, die Sie im Azure-Daten-Explorer erstellen möchten.|
    | **Event Hub-Namespace** | *AzureMonitoringData* | Der von Ihnen zuvor ausgewählte Name, der Ihren Namespace identifiziert. |
    | **Event Hub** | *insights-operational-logs* | Der von Ihnen erstellte Event Hub. |
    | **Consumergruppe** | *$Default* | Die Standardconsumergruppe. Bei Bedarf können Sie auch eine andere Consumergruppe erstellen. |
    | | |

    Zieltabelle:

    Es stehen zwei Routingoptionen zur Verfügung: *statisch* und *dynamisch*. In diesem Tutorial verwenden Sie statisches Routing (Standardeinstellung), für das der Tabellenname, das Datenformat und die Zuordnung angegeben werden müssen. Lassen Sie das Kontrollkästchen **My data includes routing info** (Meine Daten enthalten Routinginformationen) deaktiviert.

     **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**
    |---|---|---|
    | **Tabelle** | *ActivityLogsRawRecords* | Die Tabelle, die Sie in der Datenbank *TestDatabase* erstellt haben. |
    | **Datenformat** | *JSON* | Das Format, das in der Tabelle verwendet wird. |
    | **Spaltenzuordnung** | *ActivityLogsRawRecordsMapping* | Die Zuordnung, die Sie in der Datenbank *TestDatabase* erstellt haben und mit der eingehende JSON-Daten den Spaltennamen und Datentypen der Tabelle *ActivityLogsRawRecords* zugeordnet werden.|
    | | |

1. Wählen Sie **Erstellen** aus.  
---

## <a name="query-the-new-tables"></a>Abfragen der neuen Tabellen

Sie verfügen nun über eine Pipeline, durch die Daten fließen. Da die Erfassung über den Cluster standardmäßig fünf Minuten dauert, sollten Sie den Datenfluss einige Minuten lang zulassen, bevor Sie mit dem Abfragen beginnen.

# <a name="diagnostic-metrics"></a>[Diagnosemetriken](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Abfragen der Tabelle für Diagnosemetriken

Mit der folgenden Abfrage werden Daten zur Abfragedauer aus den Datensätzen von Diagnosemetrikdaten in Azure Data Explorer analysiert:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Abfrageergebnisse:

| avg_Average |
| --- |
| 00:06.156 |

# <a name="diagnostic-logs"></a>[Diagnoseprotokolle](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Abfragen der Tabelle für Diagnoseprotokolle

Diese Pipeline erzeugt Datenerfassungen über einen Event Hub. Sie überprüfen die Ergebnisse dieser Datenerfassungen.
Mit der folgenden Abfrage wird analysiert, wie viele Erfassungen in einer Minute gesammelt werden, einschließlich einer Stichprobe von `Database`, `Table` und `IngestionSourcePath` für jedes Intervall:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Abfrageergebnisse:

| count_ | any_Database | any_Table | any_IngestionSourcePath |
| --- | --- | --- | --- |
| 00:06.156 | TestDatabase | DiagnosticRawRecords | `https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip` |

# <a name="activity-logs"></a>[Aktivitätsprotokolle](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Abfragen der Tabelle für Aktivitätsprotokolle

Mit der folgenden Abfrage werden die Daten aus den Datensätzen von Aktivitätsprotokollen in Azure Data Explorer analysiert:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Abfrageergebnisse:

| avg(DurationMs) |
| --- |
| 768.333 |

---

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich im Artikel [Schreiben von Abfragen für Azure Data Explorer](write-queries.md), wie Sie viele weitere Abfragen für Daten schreiben können, die Sie aus Azure Data Explorer extrahieren.
* [Überwachen von Azure Data Explorer-Erfassungsvorgängen mithilfe von Diagnoseprotokollen](using-diagnostic-logs.md)
* [Verwenden von Metriken zum Überwachen der Clusterintegrität](using-metrics.md)
