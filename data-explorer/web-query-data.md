---
title: 'Schnellstart: Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche'
description: In diesem Schnellstart erfahren Sie, wie Sie Daten auf der Azure Data Explorer-Webbenutzeroberfläche abfragen und freigeben.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 06/15/2020
ms.localizationpriority: high
ms.openlocfilehash: 479bd512f759a20123e5eb94fcc9ec54e2a13455
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513300"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>Schnellstart: Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Azure Data Explorer stellt eine Webanwendung bereit, mit der Sie Abfragen ausführen und freigeben können. Die Anwendung steht im Azure-Portal sowie als eigenständige Webanwendung zur Verfügung. In diesem Artikel arbeiten Sie in der eigenständigen Version, mit der Sie Verbindungen mit mehreren Clustern herstellen und Deep-Links zu Ihren Abfragen freigeben können.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zusätzlich zu einem Azure-Abonnement benötigen Sie zum Abschließen dieses Schnellstarts [einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md).

## <a name="sign-in-to-the-application"></a>Anmelden bei der Anwendung

Melden Sie sich bei der [Anwendung](https://dataexplorer.azure.com/) an.

## <a name="add-clusters"></a>Hinzufügen von Clustern

Wenn Sie die Anwendung zum ersten Mal öffnen, sind keine Verbindungen vorhanden.

![Hinzufügen eines Clusters](media/web-query-data/add-cluster.png)

Sie müssen eine Verbindung mit mindestens einem Cluster hinzufügen, bevor Sie Abfragen ausführen können. In diesem Abschnitt fügen Sie Verbindungen mit dem *Hilfecluster* des Azure-Daten-Explorers, den wir als Hilfestellung beim Lernen eingerichtet haben, und mit dem Testcluster, den Sie in einem früheren Schnellstart erstellt haben, hinzu.

1. Wählen Sie in der oberen linken Ecke der Anwendung **Add cluster** (Cluster hinzufügen) aus.

1. Geben Sie im Dialogfeld **Cluster hinzufügen** den URI ein, und wählen Sie dann **Hinzufügen** aus.

   Sie können den Hilfecluster-URI `https://help.kusto.windows.net` verwenden. Wenn Sie über einen eigenen Cluster verfügen, geben Sie den URI Ihres Clusters an. Verwenden Sie beispielsweise `https://mydataexplorercluster.westus.kusto.windows.net` wie in der folgenden Abbildung:

    ![Server-URI im Portal](media/web-query-data/server-uri.png)

1. Im linken Bereich sollten jetzt der **Hilfecluster** angezeigt werden. Erweitern Sie die Datenbank **Samples**, damit Sie die Beispieltabellen sehen können, auf die Sie zugreifen können.

    ![Beispieldatenbanken](media/web-query-data/sample-databases.png)

    Wir verwenden die Tabelle **StormEvents** weiter unten in diesem Schnellstart und in anderen Artikeln zum Azure-Daten-Explorer.

Fügen Sie nun den erstellten Testcluster hinzu.

1. Wählen Sie **Cluster hinzufügen** aus.

1. Geben Sie im Dialogfeld **Cluster hinzufügen** die URL Ihres Testclusters im Format `https://<ClusterName>.<Region>.kusto.windows.net/` ein, und wählen Sie dann **Hinzufügen** aus.

    Das Beispiel unten enthält den **Hilfecluster** und einen neuen Cluster, **docscluster.westus** (vollständige URL: `https://docscluster.westus.kusto.windows.net/`).

    ![Testen des Clusters](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>Ausführen von Abfragen

Sie können jetzt Abfragen für alle Cluster ausführen, mit denen Sie verbunden sind (vorausgesetzt, in Ihrem Testcluster sind Daten vorhanden). Wir konzentrieren uns auf den **Hilfecluster**.

1. Wählen Sie im linken Bereich unter dem **Hilfecluster** die Datenbank **Samples** aus.

1. Kopieren Sie die folgende Abfrage, und fügen Sie sie im Abfragefenster ein. Wählen Sie oben im Fenster **Ausführen** aus.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Diese Abfrage gibt die zehn neuesten Datensätze in der Tabelle **StormEvents** zurück. Die linke Seite des Ergebnisses sollte der folgenden Tabelle ähneln.

    :::image type="content" source="media/web-query-data/result-set-01.png" alt-text="Screenshot: Tabelle mit Startzeit, Endzeit, Episode, Ereignis-ID, Zustand und Ereignistyp für zehn Sturmereignisse" border="false":::

    Die folgende Abbildung zeigt den Zustand, den die Anwendung nun aufweisen sollte, zusammen mit hinzugefügten Clustern und einer Abfrage mit Ergebnissen.

    ![Vollständige Anwendung](media/web-query-data/full-application.png)

1. Kopieren Sie die folgende Abfrage, und fügen Sie sie im Abfragefenster unter der ersten Abfrage ein. Beachten Sie, dass sie nicht in separaten Zeilen formatiert wird wie die erste Abfrage.

    ```Kusto
    StormEvents | sort by StartTime desc | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. Klicken Sie auf die neue Abfrage im Fenster, um sie auszuwählen. Drücken Sie UMSCHALT+ALT+F, um die Abfrage zu formatieren, sodass sie wie folgt aussieht.

    ![Formatierte Abfrage](media/web-query-data/formatted-query.png)

1. Drücken Sie UMSCHALT+EINGABE. Dies ist eine Tastenkombination zum Ausführen einer Abfrage.

   Diese Abfrage gibt dieselben Datensätze wie die erste zurück, schließt aber nur die in der `project`-Anweisung angegebenen Spalten ein. Das Ergebnis sollte der folgenden Tabelle ähneln.

    :::image type="content" source="media/web-query-data/result-set-02.png" alt-text="Screenshot: Tabelle mit Startzeit, Endzeit, Zustand, Ereignistyp, Schadenseigenschaft und Episodengeschichte für zehn Sturmereignisse" border="false":::

1. Wählen Sie oben im Abfragefenster **Abruf** aus.

    Im Abfragefenster werden nun die Ergebnisse der ersten Abfrage angezeigt, ohne dass die Abfrage erneut ausgeführt werden muss. Häufig führen Sie bei der Analyse mehrere Abfragen aus. Mit **Abruf** können Sie die Ergebnisse der vorherigen Abfragen erneut abrufen.

1. Führen Sie nun eine weitere Abfrage aus, um eine andere Art von Ausgabe zu sehen.

    ```Kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```
    Das Ergebnis sollte dem folgenden Diagramm ähneln.

    ![Säulendiagramm](media/web-query-data/column-chart.png)

> [!NOTE]
> Leere Zeilen im Abfrageausdruck können beeinflussen, welcher Teil der Abfrage ausgeführt wird.
>
> Ist kein Text ausgewählt, wird davon ausgegangen, dass die Abfrage oder der Befehl durch leere Zeilen getrennt ist.
> Ist Text ausgewählt, wird der ausgewählte Text ausgeführt.

## <a name="work-with-the-table-grid"></a>Arbeiten mit dem Tabellenraster

Nun, da Sie wissen, wie grundlegende Abfragen funktionieren, sehen wir uns an, wie Sie mit dem Tabellenraster Ergebnisse anpassen und weitere Analysen ausführen können.

1. Führen Sie die erste Abfrage erneut aus. Zeigen Sie mit der Maus auf die Spalte **State** (Bundesstaat), und wählen Sie das Menü und dann **Group by State** (Nach Bundesstaat gruppieren) aus.

    ![Nach Bundesstaat gruppieren](media/web-query-data/group-by.png)

1. Erweitern Sie im Raster **California** (Kalifornien), um Datensätze für diesen Bundesstaat anzuzeigen.

    :::image type="content" source="media/web-query-data/result-set-03.png" alt-text="Screenshot: Abfrageergebnisraster. Die Gruppe für Kalifornien ist erweitert, und drei Zeilen sind sichtbar (mit Daten von Ereignissen in Kalifornien)." border="false":::

    Diese Art der Gruppierung kann hilfreich sein, wenn Sie explorative Analysen durchführen.

1. Zeigen Sie mit der Maus auf die Spalte **Group** (Gruppe), und wählen Sie dann **Spalten zurücksetzen** aus.

    ![Spalten zurücksetzen](media/web-query-data/reset-columns.png)

    Dadurch wird das Raster auf den ursprünglichen Zustand zurückgesetzt.

1. Führen Sie die folgende Abfrage aus.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. Wählen Sie auf der rechten Seite des Rasters **Spalten** aus, um den Toolbereich anzuzeigen.

    ![Toolbereich](media/web-query-data/tool-panel.png)

    Dieser Bereich funktioniert ähnlich wie die PivotTable-Feldliste in Excel, da Sie hier weitere Analysen im Raster selbst durchführen können.

1. Wählen Sie **Pivot-Modus** aus, und ziehen Sie Spalten wie folgt: **State** nach **Row groups**, **DamageProperty** nach **Values** und **EventType** nach **Column labels**.  

    ![Pivot-Modus](media/web-query-data/pivot-mode.png)

    Das Ergebnis sollte der folgenden PivotTable ähneln.

    ![PivotTable](media/web-query-data/pivot-table.png)

    Beachten Sie, dass Vermont und Alabama jeweils zwei Ereignisse in derselben Kategorie aufweisen, während Texas zwei Ereignisse in verschiedenen Kategorien aufweist. PivotTables ermöglichen Ihnen, solche Elemente schnell zu erkennen, und sie stellen ein großartiges Tool für die schnelle Analyse dar.

## <a name="share-queries"></a>Freigeben von Abfragen

Häufig möchten Sie die erstellten Abfragen freigeben. 

1. Wählen Sie im Abfragefenster die erste Abfrage aus, die Sie hineinkopiert haben.

1. Wählen Sie oben im Abfragefenster **Freigeben** aus. 

:::image type="content" source="media/web-query-data/share-menu.png" alt-text="Menü „Freigeben“":::

Die folgenden Optionen sind im Dropdown verfügbar:
* Link in Zwischenablage
* [Link und Abfrage in Zwischenablage](#provide-a-deep-link)
* Link, Abfrage und Ergebnisse in Zwischenablage
* [An Dashboard anheften](#pin-to-dashboard)
* [Abfrage an Power BI](power-bi-imported-query.md)

### <a name="provide-a-deep-link"></a>Deep-Link bereitstellen

Sie können einen Deep-Link angeben, damit andere Benutzer mit Zugriff auf den Cluster die Abfragen ausführen können.

1. Wählen Sie in **Freigeben** den Eintrag **Link und Abfrage in Clipboard**  aus.

1. Kopieren Sie den Link und die Abfrage in eine Textdatei.

1. Fügen Sie den Link in einem neuen Browserfenster ein. Das Ergebnis sollte nach dem Ausführen der Abfrage dem folgenden ähneln.

    :::image type="content" source="media/web-query-data/shared-query.png" alt-text="Freigegebener Deep-Link der Abfrage":::

### <a name="pin-to-dashboard"></a>An Dashboard anheften

Wenn Sie die Datenuntersuchung mithilfe von Abfragen in der Webbenutzeroberfläche durchführen und die benötigten Daten finden, können Sie sie an ein Dashboard anheften, um sie fortlaufend zu überwachen. 

So heften Sie eine Abfrage an

1. Wählen Sie in **Freigeben** den Befehl **An Dashboard anheften** aus.

1. Im Bereich **An Dashboard anheften**:
    1. Geben Sie einen **Abfragenamen** ein.
    1. Wählen Sie **Vorhandenes verwenden** oder **Neu erstellen** aus.
    1. Geben Sie einen **Dashboardnamen** an.
    1. Aktivieren Sie das Kontrollkästchen **Dashboard nach Erstellung anzeigen** (wenn es sich um ein neues Dashboard handelt).
    1. Wählen Sie **Anheften** aus

    :::image type="content" source="media/web-query-data/pin-to-dashboard.png" alt-text="Bereich „An Dashboard anheften“":::
    
> [!NOTE]
> Mit **An Dashboard anheften** wird nur die ausgewählte Abfrage angeheftet. Um die Dashboarddatenquelle zu erstellen und Renderbefehle in ein visuelles Element im Dashboard zu übersetzen, muss die relevante Datenbank in der Datenbankliste ausgewählt werden.

## <a name="export-query-results"></a>Exportieren von Abfrageergebnissen

Um die Abfrageergebnisse in eine CSV-Datei zu exportieren, wählen Sie **Datei** > **Nach CSV exportieren** aus.

:::image type="content" source="media/web-query-data/export-results.png" alt-text="Exportieren von Ergebnissen in eine CSV-Datei":::

## <a name="provide-feedback"></a>Feedback geben

1. Wählen Sie rechts oben in der Anwendung das Feedbacksymbol aus. ![Feedbacksymbol](media/web-query-data/icon-feedback.png).

1. Geben Sie Ihr Feedback ein, und wählen Sie dann **Senden** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Schnellstart haben Sie keine Ressourcen erstellt, aber wenn Sie einen oder beide Cluster aus der Anwendung entfernen möchten, klicken Sie mit der rechten Maustaste auf den Cluster, und wählen Sie **Verbindung entfernen** aus.

## <a name="next-steps"></a>Nächste Schritte

[Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)
