---
title: Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)
description: In diesem Thema werden Daten in Azure Monitor abgefragt, indem ein Azure Data Explorer-Proxy für produktübergreifende Abfragen mit Application Insights und Log Analytics erstellt wird.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/28/2020
ms.localizationpriority: high
ms.openlocfilehash: cd0bc28a2d2b282c50a85c87dbf8f4989c7b4057
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513215"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)

Der Azure Data Explorer-Proxycluster (ADX Proxy) ermöglicht Ihnen das Ausführen von produktübergreifenden Abfragen zwischen Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) und [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) im Dienst [Azure Monitor](/azure/azure-monitor/). Sie können Azure Monitor den Log Analytics-Arbeitsbereichen oder Application Insights-Apps als Proxycluster zuordnen. Anschließend können Sie den Proxycluster mit Azure Data Explorer-Tools abfragen und in einer clusterübergreifenden Abfrage auf ihn verweisen. In dem Artikel wird gezeigt, wie eine Verbindung mit einem Proxycluster hergestellt wird, der Azure Data Explorer-Webbenutzeroberfläche ein Proxycluster hinzugefügt wird und über Azure Data Explorer Abfragen der AI-Apps oder LA-Arbeitsbereiche durchgeführt werden.

Abfolge der Schritte für den Azure Data Explorer-Proxy:

![Abfolge der Schritte für den ADX-Proxy](media/adx-proxy/adx-proxy-workflow.png)

## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]
> Der ADX-Proxy ist im Vorschaumodus. [Stellen Sie eine Verbindung mit dem Proxy her](#connect-to-the-proxy), um die ADX-Proxyfunktion für Ihre Cluster zu aktivieren. Wenden Sie sich mit Fragen an das Team von [ADXProxy](mailto:adxproxy@microsoft.com).

## <a name="connect-to-the-proxy"></a>Herstellen einer Verbindung mit dem Proxy

1. Überprüfen Sie vor dem Herstellen der Verbindung mit dem Log Analytics- oder Application Insights-Cluster, ob im linken Menü der native Azure Data Explorer-Cluster (z. B. der *Hilfecluster*) angezeigt wird.

    ![Nativer ADX-Cluster](media/adx-proxy/web-ui-help-cluster.png)

1. Wählen Sie auf der Azure Data Explorer-Benutzeroberfläche (https://dataexplorer.azure.com/clusters)**Cluster hinzufügen** aus.

1. Fügen Sie im Fenster **Cluster hinzufügen** dem LA- oder AI-Cluster die URL hinzu. 
    
    * Für LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Für AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Wählen Sie **Hinzufügen**.

    ![Hinzufügen eines Clusters](media/adx-proxy/add-cluster.png)

    >[!NOTE]
    >Wenn Sie eine Verbindung mit mehreren Proxyclustern hinzufügen, geben Sie jedem Cluster einen anderen Namen. Andernfalls haben alle Cluster im linken Bereich den gleichen Namen.

1. Nachdem die Verbindung hergestellt wurde, wird der LA- oder AI-Cluster im linken Bereich mit dem nativen ADX-Cluster angezeigt. 

    ![Log Analytics- und Azure Data Explorer-Cluster](media/adx-proxy/la-adx-clusters.png)

> [!NOTE]
> Die Anzahl von Azure Monitor-Arbeitsbereichen, die zugeordnet werden können, ist auf 100 beschränkt.

## <a name="run-queries"></a>Ausführen von Abfragen

Sie können die Abfragen mithilfe von Clienttools ausführen, die Kusto-Abfragen unterstützen, z. B.: Kusto Explorer, ADX-Webbenutzeroberfläche, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST-API.

> [!NOTE]
> Die ADX-Proxyfunktion wird nur für den Datenabruf verwendet. Weitere Informationen finden Sie unter [Funktionsunterstützbarkeit](#function-supportability).

> [!TIP]
> * Datenbanknamen müssen den gleichen Namen wie die im Proxycluster angegebene Ressource haben. Bei Namen wird die Groß-/Kleinschreibung beachtet.
> * Achten Sie bei clusterübergreifenden Abfragen auf eine korrekte Benennung der Application Insights-Apps und Log Analytics-Arbeitsbereiche.
> * Wenn Namen Sonderzeichen enthalten, werden diese im Proxyclusternamen durch URL-Codierung ersetzt. 
> * Wenn Namen Zeichen enthalten, die nicht den [KQL-Regeln für Bezeichnernamen](kusto/query/schema-entities/entity-names.md) entsprechen, werden sie durch einen Bindestrich ( **-** ) ersetzt.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Direkte Abfragen von Ihrem LA- oder AI-ADX-Proxycluster

Führen Sie Abfragen in Ihrem LA- oder AI-Cluster aus. Vergewissern Sie sich, dass Ihr Cluster im linken Bereich ausgewählt ist. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Abfragen des LA-Arbeitsbereichs](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Clusterübergreifende Abfragen des LA- oder AI-ADX-Clusters und des nativen ADX-Proxys

Wenn Sie über den Proxy clusterübergreifende Abfragen ausführen, überprüfen Sie, ob der native ADX-Cluster im linken Bereich ausgewählt ist. Die folgenden Beispiele veranschaulichen das Kombinieren von ADX-Clustertabellen (mithilfe von `union`) mit dem LA-Arbeitsbereich.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![Clusterübergreifende Abfragen über den Azure Data Explorer-Proxy](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

Wenn Sie anstelle von „union“ den [`join`-Operator](kusto/query/joinoperator.md) verwenden, ist möglicherweise ein [`hint`](kusto/query/joinoperator.md#join-hints) erforderlich, um die Abfrage für den nativen Azure Data Explorer-Cluster (und nicht für den Proxy) auszuführen. 

### <a name="join-data-from-an-adx-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Verknüpfen von Daten eines ADX-Clusters auf einem Mandanten mit einer Azure Monitor-Ressource auf einem anderen

Mandantenübergreifende Abfragen werden vom ADX-Proxy nicht unterstützt. Sie sind nur bei einem Mandanten angemeldet, um die Abfrage für beide Ressourcen auszuführen.

Wenn sich die Azure Data Explorer-Ressource auf Mandant „A“ und der LA-Arbeitsbereich auf Mandant „B“ befindet, sollten Sie eine der beiden folgenden Methoden verwenden:

1. Mit Azure Data Explorer können Sie Rollen für Prinzipale auf unterschiedlichen Mandanten hinzufügen. Fügen Sie Ihre Benutzer-ID auf Mandant „B“ als autorisierten Benutzer im Azure Data Explorer-Cluster hinzu. Vergewissern Sie sich, dass die Eigenschaft *[ExternalTrustedTenant](/powershell/module/az.kusto/update-azkustocluster)* im Azure Data Explorer-Cluster den Mandanten „B“ enthält. Führen Sie die übergreifende Abfrage vollständig auf Mandant „B“ aus.

2. Verwenden Sie [Lighthouse](/azure/lighthouse/), um die Azure Monitor-Ressource auf Mandant „A“ zu projizieren.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Herstellen einer Verbindung mit Azure Data Explorer-Clustern von unterschiedlichen Mandanten

Bei Kusto-Explorer werden Sie automatisch bei dem Mandanten angemeldet, zu dem das Benutzerkonto ursprünglich gehört. Für den Zugriff auf Ressourcen in anderen Mandanten mit demselben Benutzerkonto muss `tenantId` explizit in der Verbindungszeichenfolge angegeben werden: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>Funktionsunterstützbarkeit

Der Azure Data Explorer-Proxycluster unterstützt Funktionen sowohl für Application Insights als auch für Log Analytics.
Dadurch können clusterübergreifende Abfragen direkt auf eine tabellarische Azure Monitor-Funktion verweisen.
Der Proxy unterstützt die folgenden Befehle:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Die folgende Abbildung enthält ein Beispiel für das Abfragen einer tabellarischen Funktion über die Webbenutzeroberfläche von Azure Data Explorer.
Führen Sie zur Verwendung der Funktion den Namen im Abfragefenster aus.

  [ ![Abfragen einer tabellarischen Funktion über die Webbenutzeroberfläche von Azure Data Explorer](media/adx-proxy/function-query-adx-proxy.png)](media/adx-proxy/function-query-adx-proxy.png#lightbox)


## <a name="additional-syntax-examples"></a>Zusätzliche Syntaxbeispiele

Beim Aufrufen der Application Insights-Cluster (AI) oder Log Analytics-Cluster (LA) sind die folgenden Syntaxoptionen verfügbar:

|Syntaxbeschreibung  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Datenbank in einem Cluster, der nur die definierte Ressource in diesem Abonnement enthält (**empfohlen für clusterübergreifende Abfragen**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster, der alle Apps/Arbeitsbereiche in diesem Abonnement enthält    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster, der alle Apps/Arbeitsbereiche im Abonnement enthält, und diese sind Mitglieder dieser Ressourcengruppe    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster, der nur die definierte Ressource in diesem Abonnement enthält      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Nächste Schritte

[Write queries](write-queries.md) (Schreiben von Abfragen)