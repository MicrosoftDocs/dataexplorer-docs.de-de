---
title: 'Schnellstart: Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche'
description: In diesem Schnellstart erfahren Sie, wie Sie Daten auf der Azure Data Explorer-Webbenutzeroberfläche abfragen und freigeben.
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: quickstart
ms.date: 02/09/2021
ms.localizationpriority: high
ms.openlocfilehash: d581ade4a9cbba083e8cebf39a30f01586d2635c
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360145"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>Schnellstart: Abfragen von Daten auf der Azure Data Explorer-Webbenutzeroberfläche

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Mengen an Daten. Azure Data Explorer bietet eine Webbenutzeroberfläche, die Ihnen das Herstellen einer Verbindung mit Ihren Azure Data Explorer-Clustern sowie das Schreiben, Ausführen und Freigeben von Befehlen und Abfragen der Kusto-Abfragesprache ermöglicht. Die Webbenutzeroberfläche steht im Azure-Portal sowie als eigenständige Webanwendung ([Azure Data Explorer-Webbenutzeroberfläche](https://dataexplorer.azure.com)) zur Verfügung. Die Azure Data Explorer-Webbenutzeroberfläche kann auch von anderen Webportalen in einem HTML-iFrame gehostet werden. Weitere Informationen zum Hosten der Webbenutzeroberfläche und zum verwendeten Monaco-Editor finden Sie unter [Integration der Monaco-IDE](kusto/api/monaco/monaco-kusto.md).
In diesem Schnellstart arbeiten Sie in der eigenständigen Azure Data Explorer-Webbenutzeroberfläche.

:::image type="content" source="media/web-query-data/walkthrough.gif" alt-text="Exemplarische Vorgehensweise für den Kusto-Web-Explorer":::

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.
* Ein Cluster und eine Datenbank mit Daten. [Erstellen Sie einen eigenen Cluster](create-cluster-database-portal.md), oder verwenden Sie den Azure Data Explorer-Hilfecluster.

## <a name="sign-in-to-the-application"></a>Anmelden bei der Anwendung

Melden Sie sich bei der [Anwendung](https://dataexplorer.azure.com/) an.

## <a name="add-clusters"></a>Hinzufügen von Clustern

Wenn Sie die Anwendung zum ersten Mal öffnen, sind keine Clusterverbindungen vorhanden.

![Hinzufügen eines Clusters](media/web-query-data/add-cluster.png)

Sie müssen eine Verbindung mit einem Cluster hinzufügen, bevor Sie Abfragen ausführen können. In diesem Abschnitt fügen Sie Azure Data Explorer Verbindungen mit dem **Hilfecluster** und dem Testcluster, den Sie unter [Voraussetzungen](#prerequisites) erstellt haben (optional), hinzu.

### <a name="add-help-cluster"></a>Hinzufügen des Hilfeclusters

1. Wählen Sie in der oberen linken Ecke der Anwendung **Add Cluster** (Cluster hinzufügen) aus.

1. Geben Sie im Dialogfeld **Add Cluster** (Cluster hinzufügen) den URI https://help.kusto.windows.net ein, und wählen Sie dann **Add** (Hinzufügen) aus.
   
1. Im linken Bereich sollten jetzt der **Hilfecluster** angezeigt werden. Erweitern Sie die Datenbank **Samples**, und öffnen Sie den Ordner **Tables**, damit die Beispieltabellen angezeigt werden, auf die Sie zugreifen können.

    :::image type="content" source="media/web-query-data/help-cluster.png" alt-text="Suchen nach der Tabelle im Hilfecluster":::

Wir verwenden die Tabelle **StormEvents** weiter unten in diesem Schnellstart und in anderen Artikeln zum Azure-Daten-Explorer. 

### <a name="add-your-cluster"></a>Hinzufügen Ihres Clusters

Fügen Sie nun den erstellten Testcluster hinzu.

1. Wählen Sie **Add Cluster** (Cluster hinzufügen) aus.

1. Geben Sie im Dialogfeld **Add Cluster** (Cluster hinzufügen) die URL Ihres Testclusters im Format `https://<ClusterName>.<Region>.kusto.windows.net/` ein, und wählen Sie dann **Add** (Hinzufügen) aus. Verwenden Sie beispielsweise `https://mydataexplorercluster.westus.kusto.windows.net` wie in der folgenden Abbildung:

    :::image type="content" source="media/web-query-data/server-uri.png" alt-text="Eingeben der Testcluster-URL":::
    
1. Das Beispiel unten enthält den **Hilfecluster** und einen neuen Cluster, **docscluster.westus** (vollständige URL: `https://docscluster.westus.kusto.windows.net/`).

    ![Testen des Clusters](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>Ausführen von Abfragen

Sie können jetzt Abfragen für beide Cluster ausführen (vorausgesetzt, Sie haben Daten in Ihrem Testcluster). In diesem Artikel konzentrieren wir uns auf den **Hilfecluster**.

1. Wählen Sie im linken Bereich unter dem **Hilfecluster** die Datenbank **Samples** aus.

1. Kopieren Sie die folgende Abfrage, und fügen Sie sie im Abfragefenster ein. Wählen Sie oben im Fenster **Ausführen** aus.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    
    Diese Abfrage gibt die 10 neuesten Datensätze in der Tabelle **StormEvents** zurück. Das Ergebnis sollte der folgenden Tabelle ähneln.

    :::image type="content" source="media/web-query-data/result-set-take-10.png" alt-text="Screenshot: Tabelle, in der Daten für 10 Sturmereignisse aufgelistet werden." border="false":::

    Die folgende Abbildung zeigt den Zustand der Anwendung zusammen mit dem hinzugefügten Cluster und eine Abfrage mit Ergebnissen.

    :::image type="content" source="media/web-query-data/webui-take10.png" alt-text="Vollbild":::

1. Kopieren Sie die folgende Abfrage, und fügen Sie sie im Abfragefenster unter der ersten Abfrage ein. Beachten Sie, dass sie nicht in separaten Zeilen formatiert ist wie die erste Abfrage.

    ```kusto
    StormEvents | sort by StartTime desc 
    | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. Wählen Sie die neue Abfrage aus. Drücken Sie *UMSCHALT+ALT+F*, um die Abfrage zu formatieren, sodass sie wie die folgende Abfrage aussieht.

    ![Formatierte Abfrage](media/web-query-data/formatted-query.png)

1. Sie können auch **Run** (Ausführen) auswählen *UMSCHALT+EINGABETASTE* drücken, um eine Abfrage auszuführen. Diese Abfrage gibt dieselben Datensätze wie die erste zurück, schließt aber nur die in der `project`-Anweisung angegebenen Spalten ein. Das Ergebnis sollte der folgenden Tabelle ähneln.

    :::image type="content" source="media/web-query-data/result-set-project.png" alt-text="Screenshot: Tabelle mit Startzeit, Endzeit, Zustand, Ereignistyp, Schadenseigenschaft und Episodengeschichte für 10 Sturmereignisse." border="false":::

    > [!TIP]
    > Wählen Sie oben im Abfragefenster **Recall** (Abrufen) aus, um das Resultset der ersten Abfrage anzuzeigen, ohne die Abfrage erneut ausführen zu müssen. Häufig führen Sie bei der Analyse mehrere Abfragen aus. Mit **Recall** (Abrufen) können Sie die Ergebnisse der vorherigen Abfragen erneut abrufen.

1. Führen Sie nun eine weitere Abfrage aus, um eine andere Art von Ausgabe zu sehen.

    ```kusto
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
    > * Ist kein Text ausgewählt, wird davon ausgegangen, dass die Abfrage oder der Befehl durch leere Zeilen getrennt ist.
    > * Ist Text ausgewählt, wird der ausgewählte Text ausgeführt.

## <a name="work-with-the-table-grid"></a>Arbeiten mit dem Tabellenraster

Da Sie nun wissen, wie grundlegende Abfragen funktionieren, können Sie mit dem Tabellenraster Ergebnisse anpassen und weitere Analysen ausführen. 

### <a name="expand-a-cell"></a>Erweitern einer Zeile

Die Erweiterung von Zellen ist nützlich, um lange Zeichenfolgen oder dynamische Felder wie JSON-Daten anzuzeigen. 

1. Doppelklicken Sie auf eine Zelle, um eine erweiterte Ansicht zu öffnen. In dieser Ansicht können Sie lange Zeichenfolgen lesen, und es wird eine JSON-Formatierung für dynamische Daten bereitgestellt.

    :::image type="content" source="media/web-query-data/expand-cell.png" alt-text="Erweitern von Zellen auf der Azure Data Explorer-Webbenutzeroberfläche zum Anzeigen langer Zeichenfolgen":::

1. Klicken Sie in der oberen rechten Ecke des Ergebnisrasters auf das Symbol, um zwischen den Modi für den Lesebereich zu wechseln. Wählen Sie zwischen den folgenden Lesebereichsmodi für die erweiterte Ansicht: inline, unten und rechts.

    :::image type="content" source="media/web-query-data/expanded-view-icon.png" alt-text="Symbol zum Ändern des Lesebereichsmodus für die erweiterte Ansicht: Abfrageergebnisse auf der Azure Data Explorer-Webbenutzeroberfläche":::

### <a name="expand-a-row"></a>Erweitern einer Zeile

Erweitern Sie bei einer Tabelle mit Dutzenden von Spalten die gesamte Zeile, um einfach eine Übersicht der verschiedenen Spalten und ihrer Inhalte anzuzeigen. 

1. Klicken Sie links neben der Zeile, die Sie erweitern möchten, auf den Pfeil **>** .

    :::image type="content" source="media/web-query-data/expand-row.png" alt-text="Erweitern einer Zeile auf der Azure Data Explorer-Webbenutzeroberfläche":::

1. In der erweiterten Zeile sind einige Spalten erweitert (nach unten zeigender Pfeil) und einige Spalten reduziert (nach rechts zeigender Pfeil). Klicken Sie auf diese Pfeile, um zwischen diesen beiden Modi zu wechseln.

### <a name="group-column-by-results"></a>Gruppieren von Spalten nach Ergebnissen

Innerhalb der Ergebnisse können Sie Ergebnisse nach beliebigen Spalten gruppieren.

1. Führen Sie die folgende Abfrage aus:
     
    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

1. Zeigen Sie mit der Maus auf die Spalte **State** (Bundesstaat), und wählen Sie das Menü und dann **Group by State** (Nach Bundesstaat gruppieren) aus.

    ![Nach Bundesstaat gruppieren](media/web-query-data/group-by.png)

1. Doppelklicken Sie im Raster auf **California**, um Datensätze für diesen Bundesstaat zu erweitern und anzuzeigen. Diese Art der Gruppierung kann hilfreich sein, wenn Sie explorative Analysen durchführen.

    :::image type="content" source="media/web-query-data/group-expanded.png" alt-text="Screenshot: Abfrageergebnisraster mit erweiterter Gruppe „California“" border="false":::

1. Zeigen Sie mit der Maus auf die Spalte **Group** (Gruppe), und wählen Sie dann **Spalten zurücksetzen** aus. Diese Einstellung setzt das Raster auf den ursprünglichen Zustand zurück.

    ![Spalten zurücksetzen](media/web-query-data/reset-columns.png)

#### <a name="use-value-aggregation"></a>Verwenden der Wertaggregation

Nach dem Gruppieren einer Spalte können Sie mit der Funktion für die Wertaggregation einfache Statistiken pro Gruppe berechnen.

1. Wählen Sie das Menü für die Spalte aus, die Sie auswerten möchten.
1. Wählen Sie **Wertaggregation** und dann den Typ der Funktion aus, die Sie für diese Spalte ausführen möchten.

    :::image type="content" source="media/web-query-data/aggregate.png" alt-text="Aggregieren von Ergebnissen beim Gruppieren von Spalten nach Ergebnissen":::

### <a name="filter-columns"></a>Filtern von Spalten

Sie können Operatoren verwenden, um die Ergebnisse einer Spalte zu filtern.

1. Wählen Sie zum Filtern einer bestimmten Spalte das Menü für diese Spalte aus.
1. Wählen Sie das Filtersymbol aus.
1. Wählen Sie im Filter-Generator den gewünschten Operator aus.
1. Geben Sie den Ausdruck ein, nach dem Sie die Spalte filtern möchten. Die Ergebnisse werden bei der Eingabe gefiltert.
    
    > [!NOTE] 
    > Der Filter unterscheidet nicht zwischen Groß- und Kleinschreibung.

1. Um einen Filter mit mehreren Bedingungen zu erstellen, wählen Sie einen booleschen Operator aus, um eine weitere Bedingung hinzuzufügen.
1. Wenn Sie den Filter entfernen möchten, löschen Sie den Text aus der ersten Filterbedingung.

    :::image type="content" source="media/web-query-data/filter-column.gif" alt-text="GIF, das zeigt, wie eine Spalte auf der Azure Data Explorer-Webbenutzeroberfläche gefiltert wird":::

### <a name="run-cell-statistics"></a>Ausführen von Statistiken für Zellen

1. Führen Sie die folgende Abfrage aus.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. Wählen Sie im Ergebnisraster einige der numerischen Zellen aus. Das Tabellenraster ermöglicht es Ihnen, mehrere Zeilen, Spalten und Zellen auszuwählen und Aggregationen für diese zu berechnen. Die Webbenutzeroberfläche unterstützt derzeit die folgenden Funktionen für numerische Werte: **Average**, **Count**, **Min**, **Max** und **Sum**.

    :::image type="content" source="media/web-query-data/select-stats.png" alt-text="Auswählen von Funktionen"::: 

### <a name="filter-to-query-from-grid"></a>Filter für Abfrage aus dem Raster

Eine weitere einfache Möglichkeit zum Filtern des Rasters besteht darin, der Abfrage einen Filteroperator direkt aus dem Raster hinzuzufügen.

1. Wählen Sie eine Zelle mit Inhalt aus, für die Sie einen Abfragefilter erstellen möchten.
1. Klicken Sie mit der rechten Maustaste, um das Menü mit den Zellaktionen zu öffnen. Wählen Sie **Auswahl als Filter hinzufügen** aus.
    
    :::image type="content" source="media/web-query-data/add-selection-filter.png" alt-text="Hinzufügen der Auswahl als Filter auf der Azure Data Explorer-Webbenutzeroberfläche, um die Rasterergebnisse abzufragen":::

1. Eine Abfrageklausel wird der Abfrage im Abfrage-Editor hinzugefügt:

    :::image type="content" source="media/web-query-data/add-query-from-filter.png" alt-text="Hinzufügen einer Abfrageklausel aus der Filterung des Rasters auf der Azure Data Explorer-Webbenutzeroberfläche":::

### <a name="pivot"></a>Pivotieren

Die Funktion für den Pivot-Modus ähnelt der Funktion „PivotTable“ in Excel. Sie ermöglicht die Ausführung erweiterter Analysen im Raster selbst.

Mithilfe der Pivotierung können Sie einen Spaltenwert in Spalten umwandeln. Sie können beispielsweise *State* pivotieren, um Spalten für Florida, Missouri, Alabama usw. zu erstellen.

1. Wählen Sie auf der rechten Seite des Rasters **Columns** (Spalten) aus, um den Tabellentoolbereich anzuzeigen.

    ![Tabellentoolbereich](media/web-query-data/tool-panel.png)

1. Wählen Sie **Pivot-Modus** aus, und ziehen Sie Spalten wie folgt: **EventType** nach **Row groups** (Zeilengruppen), **DamageProperty** nach **Values** (Werte) und **State** nach **Column labels** (Spaltenbezeichnungen).  

    ![Pivot-Modus](media/web-query-data/pivot-mode.png)

    Das Ergebnis sollte der folgenden PivotTable ähneln:

    ![PivotTable](media/web-query-data/pivot-table.png)

## <a name="search-in-the-results-table"></a>Suchen in der Ergebnistabelle

Sie können in einer Ergebnistabelle nach einem bestimmten Ausdruck suchen.

1. Führen Sie die folgende Abfrage aus:

    ```Kusto
    StormEvents
    | where DamageProperty > 5000
    | take 1000
    ```

1. Klicken Sie rechts auf die Schaltfläche **Search** (Suchen), und geben Sie *„Wabash“* ein.

    :::image type="content" source="media/web-query-data/search.png" alt-text="Suchen in der Tabelle":::

1. Alle Erwähnungen des gesuchten Ausdrucks werden nun in der Tabelle hervorgehoben. Sie können zwischen diesen navigieren, indem Sie auf *EINGABE* klicken, um vorwärts zu navigieren, oder auf *UMSCHALT+EINGABE*, um rückwärts zu navigieren, oder Sie können die Schaltflächen *Nach oben* und *Nach unten* neben dem Suchfeld verwenden.

    :::image type="content" source="media/web-query-data/search-results.png" alt-text="Navigieren in den Suchergebnissen":::

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

1. Fügen Sie den Link in einem neuen Browserfenster ein. Das Ergebnis sollte dem folgenden Beispiel ähneln:

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

## <a name="settings"></a>Einstellungen

Auf der Registerkarte **Settings** (Einstellungen) können Sie die folgenden Aufgaben ausführen:

* [Exportieren von Umgebungseinstellungen](#export-environment-settings)
* [Importieren von Umgebungseinstellungen](#import-environment-settings)
* [Hervorheben von Fehlerebenen](#highlight-error-levels)
* [Löschen des lokalen Status](#clean-up-resources)

Wählen Sie oben rechts das Einstellungssymbol :::image type="icon" source="media/web-query-data/settings-icon.png" border="false"::: aus, um das Fenster **Settings** (Einstellungen) zu öffnen.

:::image type="content" source="media/web-query-data/settings.png" alt-text="Fenster „Einstellungen“":::

### <a name="export-and-import-environment-settings"></a>Exportieren und Importieren von Umgebungseinstellungen

Mithilfe der Export- und Importaktionen können Sie Ihre Arbeitsumgebung schützen und auf andere Browser und Geräte übertragen. Mit der Exportaktion werden alle Einstellungen, Clusterverbindungen und Abfrageregisterkarten in eine JSON-Datei exportiert, die in einen anderen Browser oder ein anderes Gerät importiert werden kann.

#### <a name="export-environment-settings"></a>Exportieren von Umgebungseinstellungen

1. Wählen Sie im Fenster **Settings** > **General** (Einstellungen > Allgemein) die Option **Export** (Exportieren) aus.
1. Die Datei **adx-export.json** wird in den lokalen Speicher heruntergeladen.
1. Wählen Sie **Clear local state** (Lokalen Status löschen) aus, um die Umgebung auf den ursprünglichen Zustand zurückzusetzen. Mit dieser Einstellung werden alle Clusterverbindungen gelöscht und geöffnete Registerkarten geschlossen.

> [!NOTE]
> **Export** (Exportieren) exportiert nur abfragebezogene Daten. In der Datei **adx-export.json** werden keine Dashboarddaten exportiert.

#### <a name="import-environment-settings"></a>Importieren von Umgebungseinstellungen

1. Wählen Sie im Fenster **Settings** > **General** (Einstellungen > Allgemein) die Option **Import** (Importieren) aus. Wählen Sie dann im Popupmenü **Warning** (Warnung) die Option **Import** (Importieren) aus.

    :::image type="content" source="media/web-query-data/import.png" alt-text="Importwarnung":::

1. Suchen Sie in Ihrem lokalen Speicher nach der Datei **adx-export.json**, und öffnen Sie sie.
1. Die vorherigen Clusterverbindungen und geöffneten Registerkarten sind jetzt verfügbar.

> [!NOTE]
> **Import** (Importieren) überschreibt alle vorhandenen Umgebungseinstellungen und Daten.

### <a name="highlight-error-levels"></a>Hervorheben von Fehlerebenen

Kusto versucht, den Schweregrad oder den Ausführlichkeitsgrad der einzelnen Zeilen im Ergebnisbereich zu interpretieren und diese dann entsprechend farbig zu kennzeichnen. Hierfür werden die einzelnen Werte der Spalten anhand von bekannten Mustern abgeglichen („Warnung“, „Fehler“ usw.). 

#### <a name="enable-error-level-highlighting"></a>Aktivieren der Hervorhebung von Fehlerebenen

So aktivieren Sie die Hervorhebung von Fehlerebenen:

1. Wählen Sie neben Ihrem Benutzernamen das Symbol **Einstellungen** aus.
1. Wählen Sie die Registerkarte **Darstellung** aus, und schieben Sie die Umschaltfläche für die Option zum **Aktivieren der Hervorhebung von Fehlerebenen** nach rechts. 

    :::image type="content" source="media/web-query-data/enable-error-highlighting.gif" alt-text="Animiertes GIF, das zeigt, wie Sie die Hervorhebung von Fehlerebenen in den Einstellungen aktivieren":::

Farbschema für Fehlerebenen im Modus **Hell** | Farbschema für Fehlerebenen im Modus **Dunkel**
|---|---|
:::image type="content" source="media/web-query-data/light-mode.png" alt-text="Screenshot der Farblegende im hellen Modus"::: | :::image type="content" source="media/web-query-data/dark-mode.png" alt-text="Screenshot der Farblegende im dunklen Modus":::

#### <a name="column-requirements-for-highlighting"></a>Spaltenanforderungen für die Hervorhebung

Bei hervorgehobenen Fehlerebenen muss die Spalte den Typ „int“, „long“ oder „string“ aufweisen. 

* Wenn die Spalte vom Typ `long` oder `int` ist:
   * Der Spaltenname muss *Ebene* lauten.
   * Werte dürfen nur Ziffern zwischen 1 und 5 enthalten.
* Wenn die Spalte vom Typ `string` ist: 
   * Der Spaltenname kann optional *Ebene* lauten, um die Leistung zu verbessern. 
   * Die Spalte darf nur die folgenden Werte enthalten:
       * critical, crit, fatal, assert, high
       * error, e
       * warning, w, monitor
       * Informationen
       * verbose, verb, d
   
## <a name="provide-feedback"></a>Feedback geben

1. Wählen Sie rechts oben in der Anwendung das Feedbacksymbol :::image type="icon" source="media/web-query-data/icon-feedback.png" border="false"::: aus.

1. Geben Sie Ihr Feedback ein, und wählen Sie dann **Senden** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Schnellstart haben Sie keine Ressourcen erstellt, aber wenn Sie einen oder beide Cluster aus der Anwendung entfernen möchten, klicken Sie mit der rechten Maustaste auf den Cluster, und wählen Sie **Verbindung entfernen** aus.
Eine andere Möglichkeit besteht darin, die Option **Clear local state** (Lokalen Status löschen) auf der Registerkarte **Settings** > **General** (Einstellungen > Allgemein) auszuwählen. Mit dieser Aktion werden alle Clusterverbindungen entfernt und alle geöffneten Abfrageregisterkarten geschlossen.

## <a name="next-steps"></a>Nächste Schritte

[Schreiben von Abfragen für den Azure-Daten-Explorer](write-queries.md)
