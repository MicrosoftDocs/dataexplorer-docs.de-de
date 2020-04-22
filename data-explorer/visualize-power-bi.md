---
title: 'Tutorial: Visualisieren von Daten aus Azure Data Explorer in Power BI'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Data Explorer mit Power BI verbinden und Daten visualisieren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: d37cef094bfc1200182f7cf23dc68d396c126eb2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81491955"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Tutorial: Visualisieren von Daten aus Azure Data Explorer in Power BI

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Power BI ist eine Business Analytics-Lösung, mit der Sie Ihre Daten visualisieren und die Ergebnisse in Ihrer Organisation teilen können. In diesem Tutorial erfahren Sie zunächst, wie Sie Visuals in Azure Data Explorer rendern. Anschließend stellen Sie mit Power BI eine Verbindung mit Azure Data Explorer her, erstellen einen Bericht anhand von Beispieldaten und veröffentlichen den Bericht im Power BI-Dienst.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Falls Sie nicht für Power BI Pro registriert sind, [registrieren Sie sich für eine kostenlose Testversion](https://app.powerbi.com/signupredirect?pbi_source=web), bevor Sie beginnen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Rendern von Visuals in Azure Data Explorer
> * Herstellen einer Verbindung mit Azure Data Explorer in Power BI Desktop
> * Arbeiten mit Daten in Power BI Desktop
> * Erstellen eines Berichts mit Visuals
> * Veröffentlichen und Freigeben des Berichts

## <a name="prerequisites"></a>Voraussetzungen

Zusätzlich zu Azure- und Power BI-Abonnements benötigen Sie für dieses Tutorial Folgendes:

* [Einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md)

* [Die StormEvents-Beispieldaten](ingest-sample-data.md) [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (wählen Sie **KOSTENLOS HERUNTERLADEN** aus)

## <a name="render-visuals-in-azure-data-explorer"></a>Rendern von Visuals in Azure Data Explorer

Bevor wir uns mit Power BI befassen, werfen wir einen Blick auf das Rendern von Visuals in Azure Data Explorer. Visuals eignen sich hervorragend für schnelle Analysen.

1. Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an.

1. Wählen Sie im linken Bereich die Testdatenbank mit den StormEvents-Beispieldaten aus.

1. Fügen Sie im rechten Fenster die folgende Abfrage ein, und klicken Sie auf **Ausführen**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Diese Abfrage zählt Wetterereignisse nach Bundesstaat. Anschließend rendert sie ein Säulendiagramm für alle Bundesstaaten mit mehr als 1.800 Wetterereignissen.

    ![Säulendiagramm der Ereignisse](media/visualize-power-bi/events-column-chart.png)

1. Fügen Sie im rechten Fenster die folgende Abfrage ein, und klicken Sie auf **Ausführen**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Diese Abfrage zählt Wetterereignisse nach dem Typ des Ereignisses für den Monat Juli im Bundesstaat Washington. Anschließend rendert sie ein Kreisdiagramm mit dem Prozentsatz für jeden Ereignistyp.

    ![Kreisdiagramm der Ereignisse](media/visualize-power-bi/events-pie-chart.png)

In Azure Data Explorer gibt es viele weitere Möglichkeiten zur Verwendung von Visuals, für einen kurzen Einblick soll dies jedoch genügen. Kommen wir nun zu Power BI.

## <a name="connect-to-azure-data-explorer"></a>Herstellen einer Verbindung mit Azure Data Explorer

Jetzt stellen Sie in Power BI Desktop eine Verbindung mit Azure Data Explorer her.

1. Wählen Sie in Power BI Desktop auf der Registerkarte **Start** die Option **Daten abrufen** und anschließend **Mehr** aus.

    ![Datensammlung](media/visualize-power-bi/get-data-more.png)

1. Suchen Sie nach *Azure Data Explorer*, und wählen Sie dann **Azure Data Explorer (Beta)** und anschließend **Verbinden** aus.

    ![Suchen und Abrufen von Daten](media/visualize-power-bi/search-get-data.png)

1. Klicken Sie auf dem Bildschirm **Connector (Vorschau)** auf **Fortfahren**.

1. Geben Sie auf dem nächsten Bildschirm den Namen Ihres Testclusters und Ihrer Testdatenbank ein. Der Cluster muss im folgenden Format angegeben werden: `https://<ClusterName>.<Region>.kusto.windows.net`. Geben Sie *StormEvents* als Namen der Tabelle ein. Übernehmen Sie für alle anderen Optionen die Standardwerte, und klicken Sie auf **OK**.

    ![Optionen für Cluster, Datenbank und Tabelle](media/visualize-power-bi/cluster-database-table.png)

1. Klicken Sie auf dem Bildschirm „Datenvorschau“ auf **Bearbeiten**.

    Die Tabelle wird im Power Query-Editor geöffnet. Dort können Sie die Zeilen und Spalten bearbeiten, bevor die Daten importiert werden.

## <a name="work-with-data-in-power-bi-desktop"></a>Arbeiten mit Daten in Power BI Desktop

Nachdem Sie eine Verbindung mit Azure Data Explorer hergestellt haben, bearbeiten Sie die Daten im Power Query-Editor. Sie löschen Zeilen mit NULL-Werten in der Spalte **BeginLat** und löschen die JSON-Spalte **StormSummary** vollständig. Dies sind einfache Vorgänge, Sie können beim Importieren von Daten aber auch komplexe Transformationen ausführen.

1. Klicken Sie auf den Pfeil für die Spalte **BeginLat**, deaktivieren Sie das Kontrollkästchen **NULL**, und klicken Sie dann auf **OK**.

    ![Filtern der Spalte](media/visualize-power-bi/filter-column.png)

1. Klicken Sie mit der rechten Maustaste auf die Spaltenüberschrift **StormSummary**, und klicken Sie anschließend auf **Entfernen**.

    ![Entfernen der Spalte](media/visualize-power-bi/remove-column.png)

1. Ändern Sie im Bereich **ABFRAGEEINSTELLUNGEN** den Namen von *Query1* in *StormEvents*.

    ![Ändern des Abfragenamens](media/visualize-power-bi/query-name.png)

1. Klicken Sie auf der Registerkarte **Start** des Menübands auf **Schließen und übernehmen**.

    ![Schließen und übernehmen](media/visualize-power-bi/close-apply.png)

    Power Query wendet die Änderungen an und importiert die Beispieldaten anschließend in ein *Datenmodell*. In den nächsten Schritten wird gezeigt, wie Sie das Modell erweitern können. Auch dies ist nur ein einfaches Beispiel, das Ihnen eine Vorstellung davon geben soll, was alles möglich ist.

1. Wählen Sie auf der linken Seite des Hauptfensters die Datenansicht aus.

    ![Datenansicht](media/visualize-power-bi/data-view.png)

1. Klicken Sie auf der Registerkarte **Modellierung** des Menübands auf **Neue Spalte**.

    ![Neue Spalte](media/visualize-power-bi/new-column.png)

1. Geben Sie die folgende DAX-Formel (Data Analysis Expressions) in der Bearbeitungsleiste ein, und drücken Sie dann die EINGABETASTE.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Bearbeitungsleiste](media/visualize-power-bi/formula-bar.png)

    Diese Formel erstellt die Spalte *DurationHours*, die die Dauer jedes Wetterereignisses in Stunden berechnet. Diese Spalte verwenden Sie im nächsten Abschnitt in einem Visual.

1. Scrollen Sie in der Tabelle nach rechts, um die Spalte anzuzeigen.

## <a name="create-a-report-with-visuals"></a>Erstellen eines Berichts mit Visuals

Nachdem die Daten importiert wurden und Sie das Datenmodell verbessert haben, können Sie einen Bericht mit Visuals erstellen. Sie fügen ein Säulendiagramm auf der Grundlage der Ereignisdauer und eine Karte, die die Ernteschäden zeigt, hinzu.

1. Wählen Sie auf der linken Seite des Fensters die Berichtsansicht aus.

    ![Berichtsansicht](media/visualize-power-bi/report-view.png)

1. Wählen Sie im Bereich **VISUALISIERUNGEN** das gruppierte Säulendiagramm aus.

    ![Hinzufügen eines Säulendiagramms](media/visualize-power-bi/add-column-chart.png)

    Ein leeres Diagramm wird der Canvas hinzugefügt.

    ![Leeres Diagramm](media/visualize-power-bi/blank-chart.png)

1. Wählen Sie in der Liste **Felder** die Einträge **DurationHours** und **State** aus.

    ![Auswählen von Feldern](media/visualize-power-bi/select-fields.png)

    Sie haben nun ein Diagramm, das die Gesamtanzahl von Stunden der Wetterereignisse nach Bundesstaat im Verlauf eines Jahrs zeigt.

    ![Säulendiagramm der Dauer der Wettereignisse](media/visualize-power-bi/duration-column-chart.png)

1. Klicken Sie außerhalb des Säulendiagramms auf eine beliebige Stelle der Canvas.

1. Klicken Sie im Bereich **VISUALISIERUNGEN** auf das Kartensymbol.

    ![Hinzufügen einer Karte](media/visualize-power-bi/add-map.png)

1. Wählen Sie in der Liste **Felder** die Einträge **CropDamage** und **State** aus. Ändern Sie die Größe der Karte, damit Sie die US-Bundesstaaten deutlich sehen können.

    ![Karte der Ernteschäden](media/visualize-power-bi/crop-damage-map.png)

    Die Größe der Blasen stellt den Dollarwert der Ernteschäden dar. Bewegen Sie den Mauszeiger über die Blasen, um Details anzuzeigen.

1. Verschieben Sie die Visuals, und ändern Sie ihre Größe, um einen Bericht zu erhalten, der der folgenden Abbildung ähnelt.

    ![Fertiger Bericht](media/visualize-power-bi/finished-report.png)

1. Speichern Sie den Bericht unter dem Namen *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Veröffentlichen und Freigeben des Berichts

Bisher haben Sie mit Power BI Desktop lokal in Power BI gearbeitet. Jetzt veröffentlichen Sie den Bericht im Power BI-Dienst, wo Sie ihn mit anderen Benutzern teilen können.

1. Klicken Sie in Power BI Desktop auf der Registerkarte **Start** auf **Veröffentlichen**.

    ![Schaltfläche "Veröffentlichen"](media/visualize-power-bi/publish-button.png)

1. Wenn Sie noch nicht bei Power BI angemeldet sind, führen Sie den Anmeldevorgang durch.

1. Klicken Sie auf **Mein Arbeitsbereich** und dann auf **Auswählen**.

    ![Arbeitsbereich auswählen](media/visualize-power-bi/select-workspace.png)

1. Wenn die Veröffentlichung abgeschlossen ist, wählen Sie **storm-events.pbix in Power BI öffnen** aus.

    ![Veröffentlichung erfolgreich](media/visualize-power-bi/publishing-succeeded.png)

    Der Bericht wird im Dienst mit den Visuals und dem Layout geöffnet, die Sie in Power BI Desktop definiert haben.

1. Klicken Sie oben rechts im Bericht auf **Freigeben**.

    ![Schaltfläche „Freigeben“](media/visualize-power-bi/share-button.png)

1. Fügen Sie auf dem Bildschirm **Bericht freigeben** einen Kollegen in Ihrer Organisation sowie einen Hinweis hinzu, und klicken Sie anschließend auf **Freigeben**.

    ![Freigeben des Berichts](media/visualize-power-bi/share-report.png)

    Wenn der Kollege die erforderlichen Berechtigungen besitzt, kann er auf den von Ihnen freigegebenen Bericht zugreifen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie den erstellten Bericht nicht aufbewahren möchten, löschen Sie einfach die Datei *storm-events.pbix*. Um den von Ihnen veröffentlichten Bericht zu löschen, gehen Sie wie folgt vor.

1. Scrollen Sie unter **Mein Arbeitsbereich** zu **BERICHTE**, und suchen Sie nach **storm-events**.

1. Klicken Sie auf die Auslassungspunkte ( **...** ) neben **storm-events**, und wählen Sie dann **Entfernen** aus.

    ![Entfernen des Berichts](media/visualize-power-bi/remove-report.png)

1. Bestätigen Sie das Entfernen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Write queries](write-queries.md) (Schreiben von Abfragen)
