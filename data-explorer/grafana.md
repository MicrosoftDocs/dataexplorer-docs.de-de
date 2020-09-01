---
title: Visualisieren von Daten über Azure Data Explorer mit Grafana
description: In diesem Artikel erfahren Sie, wie Sie den Azure Data Explorer als Datenquelle für Grafana einrichten und dann Daten aus einem Beispielcluster visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 30a78efa9cc9a54ac12eeaa6bfbdf8f5a7541eeb
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874442"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualisieren von Daten über Azure Data Explorer in Grafana

Grafana ist eine Analyseplattform, mit der Sie Daten abfragen und visualisieren sowie Dashboards auf Basis Ihrer Visualisierungen erstellen und freigeben können. Grafana stellt ein *Plug-In* für den Azure Data Explorer zur Verfügung, mit dem Sie Daten aus dem Azure Data Explorer verbinden und visualisieren können. In diesem Artikel erfahren Sie, wie Sie den Azure Data Explorer als Datenquelle für Grafana einrichten und dann Daten aus einem Beispielcluster visualisieren.

Anhand des folgenden Videos lernen Sie, wie Sie Azure Data Explorer mithilfe des Azure Data Explorer-Plugins von Grafana als Datenquelle für Grafana einrichten und die Daten anschließend visualisieren. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Alternativ können Sie die [Datenquelle auch konfigurieren](#configure-the-data-source) und die [Daten visualisieren,](#visualize-data), wie es im folgenden Artikel beschrieben ist.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um diesen Artikel durchzuarbeiten:

* [Grafana, Version 5.3.0 oder höher,](https://docs.grafana.org/installation/) für Ihr Betriebssystem

* [Azure Data Explorer-Plug-In](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) für Grafana

* Ein Cluster, der die StormEvents-Beispieldaten enthält Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Azure-Daten-Explorer-Clusters und einer Datenbank](create-cluster-database-portal.md) und [Erfassen von Beispieldaten in Azure-Daten-Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Angeben von Eigenschaften und Testen der Verbindung

Wenn der Dienstprinzipal der Rolle *Betrachter* zugeordnet ist, geben Sie jetzt Eigenschaften in Ihrer Instanz von Grafana an und testen die Verbindung zum Azure Data Explorer.

1. Wählen Sie in Grafana im linken Menü das Zahnradsymbol und dann **Datenquellen** aus.

    ![Datenquellen](media/grafana/data-sources.png)

1. Wählen Sie die Option **Datenquelle hinzufügen**.

1. Geben Sie auf der Seite **Datenquellen/Neu** einen Namen für die Datenquelle ein, und wählen Sie dann den Typ **Azure Data Explorer-Datenquelle** aus.

    ![Verbindungsname und -typ](media/grafana/connection-name-type.png)

1. Geben Sie den Namen Ihres Clusters in der Form „https://{ClusterName}.{Region}.kusto.windows.net“ ein. Geben Sie die anderen Werte aus dem Azure-Portal oder der Befehlszeilenschnittstelle ein. Eine Zuordnung finden Sie in der Tabelle unten dem folgenden Bild.

    ![Verbindungseigenschaften](media/grafana/connection-properties.png)

    | Grafana-Benutzeroberfläche | Azure-Portal | Azure-Befehlszeilenschnittstelle |
    | --- | --- | --- |
    | Subscription Id (Abonnement-ID) | ABONNEMENT-ID | SubscriptionId |
    | Mandanten-ID | Verzeichnis-ID | tenant |
    | Client-ID | Anwendungs-ID | appId |
    | Geheimer Clientschlüssel | Kennwort | password |
    | | | |

1. Wählen Sie **Speichern und testen** aus.

    Wenn der Test erfolgreich war, wechseln Sie zum nächsten Abschnitt. Sollten irgendwelche Probleme auftreten, überprüfen Sie die in Grafana angegebenen Werte, und wiederholen Sie die vorherigen Schritte.

## <a name="visualize-data"></a>Visualisieren von Daten

Nachdem Sie den Azure Data Explorer als Datenquelle für Grafana konfiguriert haben, ist es an der Zeit, Daten zu visualisieren. Hier wird ein einfaches Beispiel gezeigt, aber es sind noch viele weitere Schritte verfügbar. Es wird empfohlen, sich [Schreiben von Abfragen für den Azure Data Explorer](write-queries.md) anzusehen, um Beispiele für andere Abfragen zu erhalten, die für das Beispieldataset ausgeführt werden.

1. Wählen Sie in Grafana im linken Menü das Plussymbol und dann **Dashboard** aus.

    ![Erstellen von Dashboards](media/grafana/create-dashboard.png)

1. Wählen Sie unter der Registerkarte **Hinzufügen** die Option **Diagramm** aus.

    ![Hinzufügen des Diagramms](media/grafana/add-graph.png)

1. Wählen Sie im Diagrammpanel **Paneltitel** und dann **Bearbeiten** aus.

    ![Bearbeiten des Panels](media/grafana/edit-panel.png)

1. Wählen Sie unten im Panel **Datenquelle** und dann die Datenquelle aus, die Sie konfiguriert haben.

    ![Auswählen einer Datenquelle](media/grafana/select-data-source.png)

1. Kopieren Sie im Abfragepanel die folgende Abfrage, und wählen Sie dann **Ausführen** aus. Die Abfrage berücksichtigt die Anzahl der Ereignisse pro Tag für das Beispieldataset.

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

1. Wählen Sie im oberen Menü das Speichersymbol aus: ![Symbol „Speichern“](media/grafana/save-icon.png)erforderlich.

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
