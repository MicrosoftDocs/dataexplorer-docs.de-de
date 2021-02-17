---
title: Visualisieren von Daten über Azure Data Explorer mit Grafana
description: In diesem Artikel erfahren Sie, wie Sie den Azure Data Explorer als Datenquelle für Grafana einrichten und dann Daten aus einem Beispielcluster visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: 523fc2d9fcde2ec0626225c3179676a9cc2d653d
ms.sourcegitcommit: 18092550a9f55de314dd337b7ee7e00e8733a35f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97941300"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualisieren von Daten über Azure Data Explorer in Grafana

Grafana ist eine Analyseplattform, mit der Sie Daten abfragen und visualisieren sowie Dashboards auf Basis Ihrer Visualisierungen erstellen und freigeben können. Grafana stellt ein *Plug-In* für den Azure Data Explorer zur Verfügung, mit dem Sie Daten aus dem Azure Data Explorer verbinden und visualisieren können. In diesem Artikel erfahren Sie, wie Sie den Azure Data Explorer als Datenquelle für Grafana einrichten und dann Daten aus einem Beispielcluster visualisieren.

Anhand des folgenden Videos lernen Sie, wie Sie Azure Data Explorer mithilfe des Azure Data Explorer-Plugins von Grafana als Datenquelle für Grafana einrichten und die Daten anschließend visualisieren. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Stattdessen können Sie zum [Konfigurieren der Datenquelle](#configure-the-data-source) und zum [Visualisieren der Daten](#visualize-data) auch wie in diesem Artikel beschrieben vorgehen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um diesen Artikel durchzuarbeiten:

* [Grafana, Version 5.3.0 oder höher,](https://docs.grafana.org/installation/) für Ihr Betriebssystem

* [Azure Data Explorer-Plug-In](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) für Grafana. Für die Verwendung des Grafana-Abfrage-Generators ist mindestens die Plug-In-Version 3.0.5 erforderlich.

* Ein Cluster, der die StormEvents-Beispieldaten enthält Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md) und [Erfassen von Beispieldaten in Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Angeben von Eigenschaften und Testen der Verbindung

Wenn der Dienstprinzipal der Rolle *Betrachter* zugeordnet ist, geben Sie jetzt Eigenschaften in Ihrer Instanz von Grafana an und testen die Verbindung zum Azure Data Explorer.

1. Wählen Sie in Grafana im linken Menü das Zahnradsymbol und dann **Datenquellen** aus.

    ![Datenquellen](media/grafana/data-sources.png)

1. Wählen Sie die Option **Datenquelle hinzufügen**.

1. Geben Sie auf der Seite **Datenquellen/Neu** einen Namen für die Datenquelle ein, und wählen Sie dann den Typ **Azure Data Explorer-Datenquelle** aus.

    ![Verbindungsname und -typ](media/grafana/connection-name-type.png)

1. Geben Sie unter **Einstellungen** > **Verbindungsdetails** den Namen Ihres Clusters in der Form „https://{ClusterName}.{Region}.kusto.windows.net“ ein. Geben Sie die anderen Werte aus dem Azure-Portal oder der Befehlszeilenschnittstelle ein. Eine Zuordnung finden Sie in der Tabelle unten dem folgenden Bild.

    ![Verbindungseigenschaften](media/grafana/connection-properties.png)

    | Grafana-Benutzeroberfläche | Azure-Portal | Azure CLI |
    | --- | --- | --- |
    | Subscription Id (Abonnement-ID) | ABONNEMENT-ID | SubscriptionId |
    | Mandanten-ID | Verzeichnis-ID | tenant |
    | Client-ID | Anwendungs-ID | appId |
    | Geheimer Clientschlüssel | Kennwort | password |
    | | | |

1. Wählen Sie **Speichern und testen** aus.

    Wenn der Test erfolgreich war, wechseln Sie zum nächsten Abschnitt. Sollten irgendwelche Probleme auftreten, überprüfen Sie die in Grafana angegebenen Werte, und wiederholen Sie die vorherigen Schritte.

### <a name="optimize-queries"></a>Optimieren von Abfragen

Es gibt zwei Features, die für die Abfrageoptimierung verwendet werden können:
* [Optimieren der Dashboardrenderingleistung für Abfragen](#optimize-dashboard-query-rendering-performance-using-query-results-caching)
* [Aktivieren der schwachen Konsistenz](#enable-weak-consistency)

Nehmen Sie zum Ausführen der Optimierung unter **Datenquellen** > **Einstellungen** > **Abfrageoptimierungen** die erforderlichen Änderungen vor.

:::image type="content" source="media/grafana/query-optimization.PNG" alt-text="Bereich für die Abfrageoptimierung":::

#### <a name="optimize-dashboard-query-rendering-performance-using-query-results-caching"></a>Optimieren der Dashboardrenderingleistung für Abfragen mithilfe eines Abfrageergebniscache 

Wenn ein Dashboard oder ein visuelles Element mehrmals von mindestens einem Benutzer gerendert wird, sendet Grafana standardmäßig mindestens eine Abfrage an Azure Data Explorer. Aktivieren Sie den [Abfrageergebniscache](kusto/query/query-results-cache.md), um die Dashboardrenderingleistung zu verbessern und die Auslastung des Azure Data Explorer-Clusters zu verringern. Während des angegebenen Zeitraums verwendet Azure Data Explorer den Ergebniscache zum Abrufen der vorherigen Ergebnisse und führt keine unnötige Abfrage aus. Diese Funktion ist besonders effektiv, um die Ressourcenauslastung zu reduzieren und die Leistung zu steigern, wenn mehrere Benutzer dasselbe Dashboard verwenden.

Führen Sie zum Aktivieren des Ergebniscacherenderings im Bereich **Abfrageoptimierungen** die folgenden Schritte aus:
1. Deaktivieren Sie **Use dynamic caching** (Dynamischen Cache verwenden). 
1. Geben Sie unter **Cache Max Age** (Maximales Alter des Cache) den Zeitraum in Minuten ein, in dem Sie zwischengespeicherte Ergebnisse verwenden möchten.

#### <a name="enable-weak-consistency"></a>Aktivieren der schwachen Konsistenz

Cluster werden mit starker Konsistenz konfiguriert. Dadurch wird sichergestellt, dass Abfrageergebnisse auf dem neuesten Stand sind und alle Änderungen im Cluster beinhalten.
Wenn Sie die schwache Konsistenz aktivieren, kann bei Abfrageergebnissen nach Clusteränderungen eine Verzögerung von 1 bis 2 Minuten auftreten. Andererseits kann schwache Konsistenz das visuelle Rendering erheblich beschleunigen. Wenn die unmittelbare Konsistenz nicht entscheidend und die Leistung grenzwertig ist, aktivieren Sie schwache Konsistenz, um die Leistung zu steigern. Weitere Informationen zur Abfragekonsistenz finden Sie unter [Abfragekonsistenz](kusto/concepts/queryconsistency.md).

Wählen Sie zum Aktivieren schwacher Konsistenz im Bereich **Abfrageoptimierungen** unter **Datenkonsistenz** die Option **Schwach** aus.

## <a name="visualize-data"></a>Visualisieren von Daten

Nachdem Sie den Azure Data Explorer als Datenquelle für Grafana konfiguriert haben, ist es an der Zeit, Daten zu visualisieren. Hier finden Sie ein einfaches Beispiel, in dem sowohl der Abfrage-Generator-Modus als auch der RAW-Modus des Abfrage-Editors verwendet wird. Es wird empfohlen, sich [Schreiben von Abfragen für den Azure Data Explorer](write-queries.md) anzusehen, um Beispiele für andere Abfragen zu erhalten, die für das Beispieldataset ausgeführt werden.

1. Wählen Sie in Grafana im linken Menü das Plussymbol und dann **Dashboard** aus.

    ![Erstellen von Dashboards](media/grafana/create-dashboard.png)

1. Wählen Sie unter der Registerkarte **Hinzufügen** die Option **Diagramm** aus.

    ![Hinzufügen des Diagramms](media/grafana/add-graph.png)

1. Wählen Sie im Diagrammpanel **Paneltitel** und dann **Bearbeiten** aus.

    ![Bearbeiten des Panels](media/grafana/edit-panel.png)

1. Wählen Sie unten im Panel **Datenquelle** und dann die Datenquelle aus, die Sie konfiguriert haben.

    ![Auswählen einer Datenquelle](media/grafana/select-data-source.png)

### <a name="query-builder-mode"></a>Abfrage-Generator-Modus

Der Abfrage-Editor verfügt über zwei Modi: den Abfrage-Generator-Modus und den RAW-Modus. Verwenden Sie den Abfrage-Generator-Modus, um Ihre Abfrage zu definieren.

1. Wählen Sie unterhalb der Datenquelle die Option **Datenbank** und anschließend in der Dropdownliste Ihre Datenbank aus. 
1. Wählen Sie **Aus** und anschließend in der Dropdownliste Ihre Tabelle aus.

    :::image type="content" source="media/grafana/query-builder-from-table.png" alt-text="Auswählen der Tabelle im Abfrage-Generator":::    

1. Filtern Sie nach dem Definieren der Tabelle die Daten, wählen Sie die darzustellenden Werte aus, und definieren Sie die Gruppierung dieser Werte.

    **Filter**
    1. Klicken Sie rechts neben **Where (filter)** (Where (Filtern)) auf **+** , und wählen Sie in der Dropdownliste mindestens eine Spalte Ihrer Tabelle aus. 
    1. Definieren Sie die Werte für jeden Filter mithilfe des passenden Operators. 
    Diese Auswahl ähnelt der Verwendung des [Where-Operators](kusto/query/whereoperator.md) in der Kusto-Abfragesprache.

    **Auswählen von Werten**
    1. Klicken Sie rechts neben **Spaltenwerte** auf **+** , und wählen Sie in der Dropdownliste die Wertspalten aus, die im Bereich angezeigt werden sollen.
    1. Legen Sie für die einzelnen Wertspalten jeweils den Aggregationstyp fest. 
    Sie können eine einzelne Wertspalte oder auch mehrere Wertspalten festlegen. Diese Auswahl entspricht der Verwendung des [summarize-Operators](kusto/query/summarizeoperator.md).

    **Gruppieren von Werten** <br> 
    Klicken Sie rechts neben **Group by (summarize)** (Gruppieren nach (Zusammenfassen)) auf **+** , und wählen Sie in der Dropdownliste mindestens eine Spalte aus, die zum Gruppieren der Werte verwendet werden soll. Dies entspricht dem Gruppierungsausdruck im summarize-Operator.

1. Wählen Sie zum Ausführen der Abfrage die Option **Abfrage ausführen** aus.

    :::image type="content" source="media/grafana/query-builder-all-values.png" alt-text="Abfrage-Generator mit allen Werten":::

    > [!TIP]
    > Wenn Sie die Einstellungen im Abfrage-Generator abschließen, wird eine Abfrage in der Kusto-Abfragesprache erstellt. Diese Abfrage spiegelt die Logik wider, die Sie mithilfe des grafischen Abfrage-Editors erstellt haben. 

1. Wählen Sie **KQL bearbeiten** aus, um in den RAW-Modus zu wechseln, und bearbeiten Sie Ihre Abfrage mit der Flexibilität und Leistungsfähigkeit der Kusto-Abfragesprache.

:::image type="content" source="media/grafana/query-builder-with-raw-query.png" alt-text="Abfrage-Generator mit unformatierter Abfrage":::

### <a name="raw-mode"></a>RAW-Modus

Bearbeiten Sie die Abfrage im RAW-Modus. 

1. Fügen Sie im Abfragebereich die folgende Abfrage ein, und wählen Sie anschließend **Abfrage ausführen** aus. Die Abfrage berücksichtigt die Anzahl der Ereignisse pro Tag für das Beispieldataset.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Abfrage ausführen](media/grafana/run-query.png)

1. Das Diagramm zeigt keine Ergebnisse, da es standardmäßig auf Daten der letzten sechs Stunden ausgerichtet ist. Wählen Sie im oberen Menü **Letzte 6 Stunden** aus.

    ![Letzte sechs Stunden](media/grafana/last-six-hours.png)

1. Geben Sie einen benutzerdefinierten Bereich an, der das Jahr 2007 umfasst, das in unserem StormEvents-Beispieldataset enthalten ist. Wählen Sie **Übernehmen**.

    ![Benutzerdefinierter Datumsbereich](media/grafana/custom-date-range.png)

    Jetzt zeigt das Diagramm die nach Tag gegliederten Daten von 2007 an.

    ![Fertiges Diagramm](media/grafana/finished-graph.png)

1. Wählen Sie im oberen Menü das Speichersymbol aus: ![Symbol „Speichern“](media/grafana/save-icon.png).

> [!IMPORTANT]
> Wählen Sie **Switch to builder** (Zum Generator wechseln) aus, um in den Abfrage-Generator-Modus zu wechseln. Daraufhin wird die Abfrage von Grafana in die im Abfrage-Generator verfügbare Logik konvertiert. Die Logik des Abfrage-Generators ist beschränkt, sodass manuelle Änderungen für die Abfrage ggf. verloren gehen.

:::image type="content" source="media/grafana/raw-mode.png" alt-text="Wechseln vom RAW-Modus in den Generator-Modus":::

## <a name="create-alerts"></a>Erstellen von Warnungen

1. Wählen Sie im Startdashboard **Warnung** > **Benachrichtigungskanäle** aus, um einen neuen Benachrichtigungskanal zu erstellen.

    ![Benachrichtigungskanal erstellen](media/grafana/create-notification-channel.png)

1. Erstellen Sie einen neuen **Benachrichtigungskanal**, und wählen Sie **Speichern** aus.

    ![Erstellen eines neuen Benachrichtigungskanals](media/grafana/new-notification-channel-adx.png)

1. Wählen Sie im **Dashboard** den Eintrag **Bearbeiten** aus der Dropdownliste aus.

    ![Im Dashboard „Bearbeiten“ auswählen](media/grafana/edit-panel-4-alert.png)

1. Wählen Sie das Glockensymbol aus, um den Bereich **Warnung** zu öffnen. Wählen Sie **Warnung erstellen** aus. Füllen Sie die folgenden Eigenschaften im Bereich **Warnung** aus:

    ![Warnungseigenschaften](media/grafana/alert-properties.png)

1. Wählen Sie das Symbol **Dashboard speichern** aus, um Ihre Änderungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte

* [Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)
