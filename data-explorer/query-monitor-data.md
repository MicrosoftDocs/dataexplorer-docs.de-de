---
title: Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)
description: In diesem Thema werden Daten in Azure Monitor (Application Insights und Log Analytics) abgefragt, indem eine Azure Data Explorer-Instanz für produktübergreifende Abfragen erstellt wird.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 607bcc7b1c0401116a94a9c06ff308a06ad936b7
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371530"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)

Azure Data Explorer unterstützt dienstübergreifende Abfragen zwischen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) und [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs). Anschließend können Sie den Log Analytics- oder Application Insights-Arbeitsbereich mithilfe von Azure Data Explorer-Abfragetools und in einer dienstübergreifenden Abfrage abfragen. In diesem Artikel wird gezeigt, wie Sie eine dienstübergreifende Abfrage erstellen und den Log Analytics- oder Application Insights-Arbeitsbereich zur Azure Data Explorer-Webbenutzeroberfläche hinzufügen.

Ablauf dienstübergreifender Abfragen mit Azure Data Explorer:

![Azure Data Explorer-Proxyflow](media/query-monitor-data/query-monitor-workflow.png)

> [!NOTE]
> * Die Funktion zum Abfragen von Azure Monitor-Daten in Azure Data Explorer, entweder direkt über Azure Data Explorer-Clienttools oder indirekt durch Ausführen einer Abfrage in einem Azure Data Explorer-Cluster, befindet sich im Vorschaumodus.
>* Wenden Sie sich an das [Team für dienstübergreifende Abfragen](mailto:adxproxy@microsoft.com), wenn Sie Unterstützung benötigen.


## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Hinzufügen eines Log Analytics/Application Insights-Arbeitsbereichs zu Azure Data Explorer-Clienttools

Fügen Sie den Azure Data Explorer-Clienttools einen Log Analytics- oder Application Insights-Arbeitsbereich hinzu, um dienstübergreifende Abfragen für Ihre Cluster zu ermöglichen.

1. Überprüfen Sie vor dem Herstellen der Verbindung mit dem Log Analytics- oder Application Insights-Cluster, ob im linken Menü der native Azure Data Explorer-Cluster (z. B. der *Hilfecluster*) angezeigt wird.

    ![Nativer Azure Data Explorer-Cluster](media/query-monitor-data/web-ui-help-cluster.png)

1. Wählen Sie auf der Azure Data Explorer-Benutzeroberfläche (https://dataexplorer.azure.com/clusters)**Cluster hinzufügen** aus.

1. Fügen Sie im Fenster **Cluster hinzufügen** dem LA- oder AI-Cluster die URL hinzu.

    * Für LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Für AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

1. Wählen Sie **Hinzufügen**.

    ![Hinzufügen eines Clusters](media/query-monitor-data/add-cluster.png)

    >[!TIP]
    >Wenn Sie eine Verbindung zu mehreren Log Analytics- oder Application Insights-Arbeitsbereichen hinzufügen, geben Sie jedem einen anderen Namen. Andernfalls haben alle Cluster im linken Bereich den gleichen Namen.

1. Nachdem die Verbindung hergestellt wurde, wird Ihr Log Analytics- oder Application Insights-Arbeitsbereich im linken Bereich mit Ihrem nativen Azure Data Explorer-Cluster angezeigt.

    ![Log Analytics- und Azure Data Explorer-Cluster](media/query-monitor-data/la-adx-clusters.png)

> [!NOTE]
> Die Anzahl von Azure Monitor-Arbeitsbereichen, die zugeordnet werden können, ist auf 100 beschränkt.

## <a name="run-queries"></a>Ausführen von Abfragen

Sie können die Abfragen mithilfe von Clienttools ausführen, die Kusto-Abfragen unterstützen, z. B.: Kusto Explorer, Azure Data Explorer-Webbenutzeroberfläche, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens und REST-API.

> [!NOTE]
> Die Funktion für dienstübergreifende Abfragen wird nur für den Datenabruf verwendet. Weitere Informationen finden Sie unter [Funktionsunterstützbarkeit](#function-supportability).

> [!TIP]
> * Die Datenbank muss den gleichen Namen wie die in der dienstübergreifenden Abfrage angegebene Ressource haben. Bei Namen wird die Groß-/Kleinschreibung beachtet.
> * Achten Sie bei dienstübergreifenden Abfragen auf eine korrekte Benennung der Application Insights-Apps und Log Analytics-Arbeitsbereiche.
> * Wenn Namen Sonderzeichen enthalten, werden diese in der dienstübergreifenden Abfrage durch URL-Codierung ersetzt.
> * Wenn Namen Zeichen enthalten, die nicht den [KQL-Regeln für Bezeichnernamen](kusto/query/schema-entities/entity-names.md) entsprechen, werden sie durch einen Bindestrich ( **-** ) ersetzt.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Direkte Abfrage der Log Analytics- oder Application Insights-Arbeitsbereiche über Azure Data Explorer-Clienttools

Sie können Abfragen für Ihre Log Analytics- oder Application Insights-Arbeitsbereiche über Azure Data Explorer-Clienttools ausführen. 

1. Vergewissern Sie sich, dass Ihr Arbeitsbereich im linken Bereich ausgewählt ist.

1. Führen Sie die folgende Abfrage aus:

```kusto
Perf | take 10 // Demonstrate cross-service query on the Log Analytics workspace
```

![Abfragen eines Log Analytics-Arbeitsbereichs](media/query-monitor-data/query-la.png)

### <a name="cross-query-of-your-log-analytics-or-application-insights-workspace-and-the-azure-data-explorer-native-cluster"></a>Übergreifende Abfrage von Log Analytics- oder Application Insights-Arbeitsbereichen und des nativen Azure Data Explorer-Clusters

Wenn Sie clusterübergreifende Dienstabfragen ausführen, überprüfen Sie, ob der native Azure Data Explorer-Cluster im linken Bereich ausgewählt ist. In den folgenden Beispielen wird veranschaulicht, wie Sie Azure Data Explorer-Clustertabellen (mithilfe von `union`) mit einem Log Analytics-Arbeitsbereich kombinieren.

Führen Sie die folgenden Abfragen aus:

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Dienstübergreifende Abfrage über Azure Data Explorer](media/query-monitor-data/cross-query.png)](media/query-monitor-data/cross-query.png#lightbox)

> [!TIP]
> Wenn Sie anstelle von „union“ den [`join`-Operator](kusto/query/joinoperator.md) verwenden, ist möglicherweise ein [`hint`](kusto/query/joinoperator.md#join-hints) erforderlich, um die Abfrage für einen nativen Azure Data Explorer-Cluster auszuführen.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Verknüpfen von Daten eines Azure Data Explorer-Clusters in einem Mandanten mit einer Azure Monitor-Ressource in einem anderen Mandanten

Mandantenübergreifende Abfragen zwischen den Diensten werden nicht unterstützt. Sie sind nur bei einem Mandanten angemeldet, um die Abfrage für beide Ressourcen auszuführen.

Wenn sich die Azure Data Explorer-Ressource im Mandanten „A“ und der Log Analytics-Arbeitsbereich im Mandanten „B“ befindet, sollten Sie eine der beiden folgenden Methoden verwenden:

1. Mit Azure Data Explorer können Sie Rollen für Prinzipale auf unterschiedlichen Mandanten hinzufügen. Fügen Sie Ihre Benutzer-ID auf Mandant „B“ als autorisierten Benutzer im Azure Data Explorer-Cluster hinzu. Vergewissern Sie sich, dass die Eigenschaft *[ExternalTrustedTenant](/powershell/module/az.kusto/update-azkustocluster)* im Azure Data Explorer-Cluster den Mandanten „B“ enthält. Führen Sie die übergreifende Abfrage vollständig auf Mandant „B“ aus.

2. Verwenden Sie [Lighthouse](/azure/lighthouse/), um die Azure Monitor-Ressource auf Mandant „A“ zu projizieren.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Herstellen einer Verbindung mit Azure Data Explorer-Clustern von unterschiedlichen Mandanten

Bei Kusto-Explorer werden Sie automatisch bei dem Mandanten angemeldet, zu dem das Benutzerkonto ursprünglich gehört. Für den Zugriff auf Ressourcen in anderen Mandanten mit demselben Benutzerkonto muss `tenantId` explizit in der Verbindungszeichenfolge angegeben werden: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Funktionsunterstützbarkeit

Dienstübergreifende Abfragen mit Azure Data Explorer unterstützen Funktionen sowohl für Application Insights als auch für Log Analytics.
Dadurch können clusterübergreifende Abfragen direkt auf eine tabellarische Azure Monitor-Funktion verweisen.
Die folgenden Befehle werden bei dienstübergreifenden Abfragen unterstützt:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Die folgende Abbildung enthält ein Beispiel für das Abfragen einer tabellarischen Funktion über die Webbenutzeroberfläche von Azure Data Explorer.
Führen Sie zur Verwendung der Funktion den Namen im Abfragefenster aus.

  [ ![Abfragen einer tabellarischen Funktion über die Webbenutzeroberfläche von Azure Data Explorer](media/query-monitor-data/function-query.png)](media/query-monitor-data/function-query.png#lightbox)

## <a name="additional-syntax-examples"></a>Zusätzliche Syntaxbeispiele

Beim Aufrufen der Application Insights- oder Log Analytics-Cluster sind die folgenden Syntaxoptionen verfügbar:

|Syntaxbeschreibung  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Datenbank in einem Cluster, der nur die definierte Ressource in diesem Abonnement enthält (**empfohlen für clusterübergreifende Abfragen**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster, der alle Apps/Arbeitsbereiche in diesem Abonnement enthält    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster, der alle Apps/Arbeitsbereiche im Abonnement enthält, und diese sind Mitglieder dieser Ressourcengruppe    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster, der nur die definierte Ressource in diesem Abonnement enthält      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Nächste Schritte

[Write queries](write-queries.md) (Schreiben von Abfragen)