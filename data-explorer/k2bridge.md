---
title: Visualisieren von Daten über Azure Data Explorer mit Kibana
description: In diesem Artikel erfahren Sie, wie Sie Azure Data Explorer als Datenquelle für Kibana einrichten.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 0d6695ddf6923dcbf44ac3466a2388edc7618551
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874969"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Visualisieren von Daten aus Azure Data Explorer in Kibana mit dem Open-Source-Connector K2Bridge

K2Bridge (Kibana-Kusto Bridge) ermöglicht es Ihnen, Azure Data Explorer als Datenquelle zu verwenden und diese Daten in Kibana zu visualisieren. K2Bridge ist eine [Open-Source](https://github.com/microsoft/K2Bridge)-Containeranwendung. Sie fungiert als Proxy zwischen einer Kibana-Instanz und einem Azure Data Explorer-Cluster. In diesem Artikel wird beschrieben, wie Sie diese Verbindung mit K2Bridge erstellen.

K2Bridge übersetzt Kibana-Abfragen in die Kusto-Abfragesprache (KQL) und sendet die Azure Data Explorer-Ergebnisse zurück an Kibana.

   ![Kibana-Verbindung mit Azure Data Explorer über K2Bridge](media/k2bridge/k2bridge-chart.png)

K2Bridge unterstützt die Kibana-Registerkarte **Discover** (Entdecken), auf der Sie folgende Möglichkeiten haben:

* Daten suchen und untersuchen
* Ergebnisse filtern
* Felder im Ergebnisraster hinzufügen oder entfernen
* Datensatzinhalt anzeigen
* Suchvorgänge speichern und freigeben

Die folgende Abbildung zeigt eine Kibana-Instanz, die über K2Bridge an Azure Data Explorer gebunden ist. Die Benutzeroberfläche in Kibana ist unverändert.

   [![An Azure Data Explorer gebundene Kibana-Seite](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Daten aus Azure Data Explorer in Kibana visualisieren können, halten Sie Folgendes bereit:

* [Helm v3](https://github.com/helm/helm#install). Dabei handelt es sich um den Kubernetes-Paket-Manager.

* AKS-Cluster (Azure Kubernetes Service) oder einen beliebigen anderen Kubernetes-Cluster. Die Versionen 1.14 bis 1.16 wurden getestet und überprüft. Wenn Sie einen AKS-Cluster benötigen, lesen Sie die Informationen zum Bereitstellen eines AKS-Clusters [mithilfe der Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) oder [über das Azure-Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal).

* [Azure Data Explorer-Cluster](create-cluster-database-portal.md), einschließlich der URL und des Datenbanknamens des Clusters

* Azure AD-Dienstprinzipal (Azure Active Directory), der berechtigt ist, Daten in Azure Data Explorer anzuzeigen, einschließlich der Client-ID und des geheimen Clientschlüssels

    Wir empfehlen die Verwendung eines Dienstprinzipals mit Anzeigeberechtigung und raten von der Verwendung höherer Berechtigungen ab. [Legen Sie die Anzeigeberechtigung des Clusters für den Azure AD-Dienstprinzipal fest](manage-database-permissions.md#manage-permissions-in-the-azure-portal).

    Weitere Informationen über den Azure AD-Dienstprinzipal finden Sie unter [Erstellen eines Azure AD-Dienstprinzipals](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Ausführen von K2Bridge für Azure Kubernetes Service (AKS)

Standardmäßig verweist das Helm-Diagramm von K2Bridge auf ein öffentlich verfügbares Image, das sich in Microsoft Container Registry (MCR) befindet. Für MCR sind keine Anmeldeinformationen erforderlich.

1. Laden Sie die erforderlichen Helm-Diagramme herunter.

1. Fügen Sie die Elasticsearch-Abhängigkeit zu Helm hinzu. Die Abhängigkeit ist erforderlich, da K2Bridge eine kleine interne Elasticsearch-Instanz verwendet. Die Instanz bedient metadatenbezogene Anforderungen (etwa Abfragen von Indexmustern und gespeicherte Abfragen). Diese interne Instanz speichert keine Geschäftsdaten. Sie können die Instanz als Implementierungsdetail betrachten.

    1. Führen Sie zum Hinzufügen der Elasticsearch-Abhängigkeit zu Helm die folgenden Befehle aus:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. So rufen Sie das K2Bridge-Diagramm aus dem Diagrammverzeichnis des GitHub-Repositorys ab:

        1. Klonen Sie das Repository von [GitHub](https://github.com/microsoft/K2Bridge).
        1. Wechseln Sie zum Stammverzeichnis des K2Bridges-Repositorys.
        1. Führen Sie den folgenden Befehl aus:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Stellen Sie K2Bridge bereit.

    1. Legen Sie für die Variablen die richtigen Werte für Ihre Umgebung fest:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Aktivieren Sie optional Azure Application Insights-Telemetrie. Wenn Sie Azure Application Insights zum ersten Mal verwenden, sollten Sie zunächst eine [Application Insights-Ressource erstellen](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). [Kopieren Sie den Instrumentierungsschlüssel](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) in eine Variable:

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a> Installieren Sie das K2Bridge-Diagramm:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        Unter [Configuration](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) (Konfiguration) finden Sie den vollständigen Satz von Konfigurationsoptionen.

    1. <a name="install-kibana-service"></a> In der Ausgabe des vorherigen Befehls wird der nächste Helm-Befehl zum Bereitstellen von Kibana vorgeschlagen. Führen Sie optional den folgenden Befehl aus:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```

    1. Verwenden Sie Portweiterleitung, um über Localhost auf Kibana zuzugreifen:

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```

    1. Stellen Sie eine Verbindung mit Kibana her, indem Sie zu http://127.0.0.1:5601 navigieren.

    1. Machen Sie Kibana für Benutzer verfügbar. Dazu gibt es mehrere Möglichkeiten. Die von Ihnen verwendete Methode hängt weitgehend von Ihrem Anwendungsfall ab.

        Beispielsweise können Sie den Dienst als Load Balancer-Dienst verfügbar machen. Fügen Sie dazu dem oben verwendeten [Installationsbefehl (**install**) von Kibana Helm](#install-kibana-service) den Parameter **--set service.type=LoadBalancer** hinzu.

        Führen Sie dann den folgenden Befehl aus:

        ```bash
        kubectl get service -w -n k2bridge
        ```

        Die Ausgabe sollte wie folgt aussehen:

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx    <pending>     5601:30128/TCP   4m24s
        ```

        Anschließend können Sie den angezeigten generierten Wert für „EXTERNAL-IP“ verwenden. Verwenden Sie ihn für den Zugriff auf Kibana. Öffnen Sie dazu einen Browser, und navigieren Sie zu „\<EXTERNAL-IP\>:5601“.

1. Konfigurieren Sie Indexmuster für den Zugriff auf Ihre Daten.

    In einer neuen Kibana-Instanz:

    1. Öffnen Sie Kibana.
    1. Navigieren Sie zu **Management** (Verwaltung).
    1. Wählen Sie **Indexmuster** aus.
    1. Erstellen Sie ein Indexmuster. Der Name des Index muss genau mit dem Tabellennamen oder dem Funktionsnamen ohne Sternchen (\*) übereinstimmen. Sie können die entsprechende Zeile aus der Liste kopieren.

> [!Note]
> Ändern Sie für die Ausführung von K2Bridge unter anderen Kubernetes-Anbietern den Wert für **storageClassName** von Elasticsearch in „values.yaml“, damit er dem vom Anbieter vorgeschlagenen Wert entspricht.

## <a name="visualize-data"></a>Visualisieren von Daten

Wenn Azure Data Explorer als Datenquelle für Kibana konfiguriert ist, können Sie die Daten mit Kibana untersuchen.

1. Wählen Sie in Kibana im Menü ganz links die Registerkarte **Discover** (Entdecken) aus.

1. Wählen Sie im Dropdown-Listenfeld ganz links ein Indexmuster aus. Das Muster definiert die Datenquelle, die Sie untersuchen möchten. In diesem Fall ist das Indexmuster eine Azure Data Explorer-Tabelle.

   ![Auswählen eines Indexmusters](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Wenn Ihre Daten ein Zeitfilterfeld aufweisen, können Sie den Zeitbereich angeben. Wählen Sie oben rechts auf der Seite **Discover** (Entdecken) einen Zeitfilter aus. Standardmäßig werden auf der Seite Daten für die letzten 15 Minuten angezeigt.

   ![Auswählen eines Zeitfilters](media/k2bridge/k2bridge-time-filter.png)

1. Die Ergebnistabelle zeigt die ersten 500 Datensätze. Sie können ein Dokument erweitern, um seine Felddaten entweder im JSON- oder im Tabellenformat zu untersuchen.

   ![Ein erweiterter Datensatz](media/k2bridge/k2bridge-expand-record.png)

1. Die Ergebnistabelle enthält standardmäßig die Spalte **_source**. Sie enthält außerdem die Spalte **Time**, falls ein Zeitfeld vorhanden ist. Sie können der Ergebnistabelle bestimmte Spalten hinzufügen, indem Sie im Bereich ganz links neben dem Feldnamen die Option **add** (Hinzufügen) auswählen.

   ![Bestimmte Spalten mit hervorgehobener Schaltfläche „add“ (Hinzufügen)](media/k2bridge/k2bridge-specific-columns.png)

1. In der Abfrageleiste können Sie die Daten wie folgt durchsuchen:

    * Eingeben eines Suchbegriffs
    * Verwenden der Lucene-Abfragesyntax Beispiel:
        * Suchen Sie nach „Fehler“, um alle Datensätze zu finden, die diesen Wert enthalten.
        * Suchen Sie nach „Status: 200“, um alle Datensätze mit dem Statuswert 200 abzurufen.
    * Verwenden der logischen Operatoren **AND**, **OR** und **NOT**
    * Verwenden der Platzhalterzeichen Sternchen (\*) und Fragezeichen (?). Beispiel: Die Abfrage „destination_city: L*“ entspricht Datensätzen, bei denen der Wert des Zielorts mit „L“ oder „l“ beginnt. (Bei K2Bridge wird keine Groß-/Kleinschreibung beachtet.)

    ![Ausführen einer Abfrage](media/k2bridge/k2bridge-run-query.png)

    > [!Tip]
    > In [Suchen](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md) finden Sie weitere Suchregeln und Logik.

1. Verwenden Sie die Feldliste im Bereich ganz rechts auf der Seite, um Ihre Suchergebnisse zu filtern. In der Feldliste können Sie Folgendes sehen:

    * Die fünf wichtigsten Werte für das Feld
    * Die Anzahl von Datensätzen, die das Feld enthalten
    * Der Prozentsatz der Datensätze, die die einzelnen Werte enthalten.

    >[!Tip]
    > Verwenden Sie die Lupe, um alle Datensätze zu suchen, die einen bestimmten Wert aufweisen.

    ![Eine Feldliste mit hervorgehobener Lupe](media/k2bridge/k2bridge-field-list.png)

    Sie können die Lupe auch verwenden, um Ergebnisse zu filtern und die Formatansicht der Ergebnistabelle für die einzelnen Datensätze in der Ergebnistabelle anzuzeigen.

     ![Eine Tabellenliste mit hervorgehobener Lupe](media/k2bridge/k2bridge-table-list.png)

1. Wählen Sie entweder **Save** (Speichern) oder **Share** (Freigeben) für Ihre Suche aus.

     ![Hervorgehobene Schaltflächen zum Speichern oder Freigeben der Suche](media/k2bridge/k2bridge-save-search.png)
