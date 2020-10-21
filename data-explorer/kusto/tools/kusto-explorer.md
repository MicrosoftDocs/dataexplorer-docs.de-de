---
title: Installation und Benutzeroberfläche von Kusto.Explorer
description: Enthält eine Beschreibung der Features von Kusto.Explorer und der Nutzung für die Erkundung Ihrer Daten.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: a424e34561792287d6e761f8d5b0747fbf8c7100
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342789"
---
# <a name="kustoexplorer-installation-and-user-interface"></a>Installation und Benutzeroberfläche von Kusto.Explorer

Kusto.Explorer ist eine umfassende Desktopanwendung zum Erkunden Ihrer Daten, bei der die Kusto-Abfragesprache auf einer benutzerfreundlichen Oberfläche genutzt wird. In diesem Übersichtsartikel wird beschrieben, wie Sie mit dem Einrichten Ihrer Kusto.Explorer-Anwendung beginnen und wie Sie die Benutzeroberfläche verwenden.

Mit Kusto.Explorer haben Sie die folgenden Möglichkeiten:
* [Abfragen Ihrer Daten](kusto-explorer-using.md#query-mode)
* [Durchsuchen Ihrer Daten](kusto-explorer-using.md#search-mode) in mehreren Tabellen
* [Visualisieren Ihrer Daten](#visualizations-section) mit vielen verschiedenen Graphen
* [Freigeben von Abfragen und Ergebnissen](kusto-explorer-using.md#share-queries-and-results) per E-Mail oder mit Deep-Links

## <a name="installing-kustoexplorer"></a>Installieren von Kusto.Explorer

* Laden Sie das Kusto.Explorer-Tool über die folgenden Links herunter, und installieren Sie es:
     * [https://aka.ms/ke](https://aka.ms/ke) (CDN-Adresse)
     * [https://aka.ms/ke-mirror](https://aka.ms/ke-mirror) (keine CDN-Adresse)

* Alternativ können Sie auch mit Ihrem Browser unter der folgenden URL auf Ihren Kusto-Cluster zugreifen: `https://<your_cluster>.<region>.kusto.windows.net.`
   Ersetzen Sie hierbei &lt;your_cluster&gt; und &lt;region&gt; durch den Namen und die Bereitstellungsregion Ihres Azure Data Explorer-Clusters.

### <a name="using-chrome-and-kustoexplorer"></a>Verwenden von Chrome und Kusto.Explorer

Stellen Sie bei Verwendung von Chrome als Standardbrowser sicher, dass Sie die ClickOnce-Erweiterung für Chrome installieren:

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>Übersicht über die Benutzeroberfläche

Die Benutzeroberfläche von Kusto.Explorer verfügt über ein Layout mit Registerkarten und Bereichen, das denen der anderen Microsoft-Produkte ähnelt: 

1. Navigieren Sie im [Menübereich](#menu-panel) durch die Registerkarten, um verschiedene Vorgänge durchzuführen.
2. Verwalten Sie Ihre Verbindungen im [Verbindungsbereich](#connections-panel).
3. Erstellen Sie die auszuführenden Skripts im Skriptbereich.
4. Überprüfen Sie die Ergebnisse der Skripts im Ergebnisbereich.

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto.Explorer: Start":::

## <a name="menu-panel"></a>Menübereich

Der Menübereich von Kusto.Explorer enthält die folgenden Registerkarten:

* [Home](#home-tab)
* [Datei](#file-tab)
* [Verbindungen](#connections-tab)
* [Anzeigen](#view-tab)
* [Extras](#tools-tab)
* [Überwachung](#monitoring-tab)
* [Verwaltung](#management-tab)
* [Hilfe](#help-tab)

### <a name="home-tab"></a>Registerkarte "Home"

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Kusto.Explorer: Start":::

Auf der Registerkarte „Start“ werden die zuletzt verwendeten Funktionen in verschiedenen Abschnitten angezeigt:

* [Abfrage](#query-section)
* [Teilen](#share-section)
* [Visualisierungen](#visualizations-section)
* [Anzeigen](#view-section)
* [Hilfe](#help-tab) 

### <a name="query-section"></a>Abschnitt „Abfrage“

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="Kusto.Explorer: Start":::

|Menü|    Verhalten|
|----|----------|
|Dropdownliste „Modus“ | <ul><li>Abfragemodus: Schaltet das Abfragefenster in den [Skriptmodus](kusto-explorer-using.md#query-mode) um. Befehle können geladen und als Skripts gespeichert werden (Standardeinstellung).</li> <li> Suchmodus: Ein Modus für einzelne Abfragen, in dem jeder eingegebene Befehl sofort verarbeitet und im Ergebnisfenster das entsprechende Ergebnis dazu angezeigt wird.</li> <li>Erweiterter Suchmodus (Search++): Ermöglicht das Suchen nach einem Begriff mithilfe von Suchsyntax in einer oder mehreren Tabellen. Erfahren Sie mehr zum [erweiterten Suchmodus (Search++)](kusto-explorer-using.md#search-mode).</li></ul> |
|Neue Registerkarte| Öffnet eine neue Registerkarte für Kusto-Abfragen. |

### <a name="share-section"></a>Abschnitt „Freigabe“

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Kusto.Explorer: Start":::

|Menü|    Verhalten|
|----|----------|
|Data To Clipboard (Daten in Zwischenablage)|    Exportiert die Abfrage und das Dataset in die Zwischenablage. Bei der Anzeige eines Diagramms werden die Diagrammdaten als Bitmap exportiert.| 
|Result To Clipboard (Ergebnis in Zwischenablage)| Exportiert das Dataset in die Zwischenablage. Bei der Anzeige eines Diagramms werden die Diagrammdaten als Bitmap exportiert.| 
|Query to Clipboard (Abfrage in Zwischenablage)| Exportiert die Abfrage in die Zwischenablage.|

### <a name="visualizations-section"></a>Abschnitt „Visualisierungen“

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Kusto.Explorer: Start":::

|Menü         | Verhalten|
|-------------|---------|
|Flächendiagramm      | Zeigt ein Flächendiagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden unterschiedlichen Serien (Y-Achse) zugeordnet. |
|Säulendiagramm | Zeigt ein Säulendiagramm an, in dem alle numerischen Spalten unterschiedlichen Serien (Y-Achse) zugeordnet sind. Die Textspalte vor der numerischen Spalte ist die X-Achse (kann auf der Benutzeroberfläche gesteuert werden).|
|Balkendiagramm    | Zeigt ein Balkendiagramm an, in dem alle numerischen Spalten unterschiedlichen Serien (X-Achse) zugeordnet sind. Die Textspalte vor der numerischen Spalte ist die Y-Achse (kann auf der Benutzeroberfläche gesteuert werden).|
|Gestapeltes Flächendiagramm      | Zeigt ein gestapeltes Flächendiagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden unterschiedlichen Serien (Y-Achse) zugeordnet. |
|Zeitachsendiagramm   | Zeigt ein Zeitachsendiagramm an, in dem die X-Achse die erste Spalte ist (muss „datetime“ sein). Alle numerischen Spalten werden unterschiedlichen Serien (Y-Achse) zugeordnet.|
|Liniendiagramm   | Zeigt ein Liniendiagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden unterschiedlichen Serien (Y-Achse) zugeordnet.|
|[Anomaliediagramm](#anomaly-chart)|    Ähnelt dem Zeitdiagramm, dient aber zum Ermitteln von Anomalien in Zeitreihendaten, indem der Algorithmus für Machine Learning-Anomalien verwendet wird. Für die Anomalieerkennung wird in Kusto.Explorer die Funktion [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) verwendet.
|Kreisdiagramm    |    Zeigt ein Kreisdiagramm an, in dem die Farbachse die erste Spalte ist. Die Theta-Achse (muss ein Measure mit Konvertierung in Prozent sein) ist die zweite Spalte.|
|Time Ladder (Zeitleiter) |    Zeigt ein Reihenfolgediagramm (Ladder Chart) an, in dem die X-Achse den letzten beiden Spalten entspricht (muss „datetime“ sein). Die Y-Achse setzt sich aus den anderen Spalten zusammen.|
|Punktdiagramm| Zeigt ein Punktdiagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden unterschiedlichen Serien (Y-Achse) zugeordnet.|
|PivotChart  | Zeigt eine PivotTable und ein PivotChart an, um die flexible Auswahl von Daten, Spalten, Zeilen und unterschiedlichen Diagrammtypen zu ermöglichen.| 
|Time Pivot (PivotChart für Zeit)   | Interaktive Navigation durch die Ereignisse der Zeitachse (Pivotierung auf der Zeitachse).|

> [!NOTE]
> <a id="anomaly-chart">Anomaliediagramm</a>: Der Algorithmus erwartet Zeitreihendaten, die aus zwei Spalten bestehen:
>* Zeit in Buckets mit festem Intervall
>* Numerischer Wert für Anomalieerkennung. Führen Sie zum Erzeugen von Zeitreihendaten in Kusto.Explorer die Zusammenfassung nach dem Zeitfeld durch, und geben Sie den Bucket für den Zeitabschnitt an.

### <a name="view-section"></a>Abschnitt „Ansicht“

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Kusto.Explorer: Start":::

|Menü           | Verhalten|
|---------------|---------|
|Full View Mode (Vollbildmodus) | Maximiert den Arbeitsbereich, indem das Menüband und der Verbindungsbereich ausgeblendet werden. Beenden Sie den Vollbildmodus, indem Sie **Start** > **Full View Mode** (Vollbildmodus) auswählen oder **F11** drücken.|
|Hide Empty Columns (Leere Spalten ausblenden)| Entfernt leere Spalten aus dem Datenraster.|
|Collapse Singular Columns (Einzelne Spalten reduzieren)| Reduziert Spalten, die einzelne Werte enthalten.|
|Explore Column Values (Spaltenwerte untersuchen)| Zeigt die Verteilung der Spaltenwerte an.|
|Schriftgrad vergrößern  | Vergrößert den Schriftgrad der Registerkarte „Abfrage“ und des Rasters mit den Ergebnisdaten.|  
|Schriftgrad verkleinern  | Verkleinert den Schriftgrad der Registerkarte „Abfrage“ und des Rasters mit den Ergebnisdaten.|

>[!NOTE]
> Einstellungen für die Datenansicht:
>
> Mit Kusto.Explorer wird nachverfolgt, welche Einstellungen für einen eindeutigen Satz mit Spalten verwendet werden. Wenn Spalten neu sortiert oder entfernt werden, wird die Datenansicht gespeichert und jeweils wiederverwendet, wenn die Daten mit den gleichen Spalten abgerufen werden. Wählen Sie auf der Registerkarte **Ansicht** die Option **Sicht zurücksetzen** aus, um die Ansicht auf die Standardwerte zurückzusetzen. 

## <a name="file-tab"></a>Registerkarte „Datei“

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Kusto.Explorer: Start":::

|Menü| Verhalten|
|---------------|---------|
||---------*Abfrageskript*---------|
|Neue Registerkarte | Öffnet ein neues Registerkartenfenster für Kusto-Abfragen. |
|Datei öffnen| Lädt Daten aus einer KQL-Datei in den aktiven Skriptbereich.|
|In Datei speichern| Speichert den Inhalt des aktiven Skriptbereichs in der KQL-Datei.|
|Registerkarte schließen| Schließt das aktuelle Registerkartenfenster.|
||---------*Daten speichern*---------|
|Data To CSV (Daten als CSV)       | Exportiert Daten in eine CSV-Datei (durch Trennzeichen getrennte Werte).| 
|Data To JSON (Daten als JSON)      | Exportiert Daten in eine Datei im JSON-Format.|
|Data To Excel (Daten nach Excel)     | Exportiert Daten in eine XLSX-Datei (Excel).|
|Data To Text (Daten als Text)      | Exportiert Daten in eine TXT-Datei (Text).| 
|Data To KQL Script (Daten als KQL-Skript)| Exportiert eine Abfrage in eine Skriptdatei.| 
|Data To Results (Daten als Ergebnisse)   | Exportiert Abfragen und Daten in eine Ergebnisdatei (QRES).|
|Run Query Into CSV (Abfrage in CSV ausführen) |Führt eine Abfrage aus und speichert die Ergebnisse in einer lokalen CSV-Datei.|
||---------*Daten laden*---------|
|From Results (Aus Ergebnissen)|    Lädt Abfragen und Daten aus einer Ergebnisdatei (QRES).| 
||---------*Zwischenablage*---------|
|Query and Results To Clipboard (Abfragen und Ergebnisse in Zwischenablage)|    Exportiert die Abfrage und das Dataset in die Zwischenablage. Bei der Anzeige eines Diagramms werden die Diagrammdaten als Bitmap exportiert.| 
|Result To Clipboard (Ergebnis in Zwischenablage)| Exportiert das Dataset in die Zwischenablage. Bei der Anzeige eines Diagramms werden die Diagrammdaten als Bitmap exportiert.| 
|Query to Clipboard (Abfrage in Zwischenablage)| Exportiert die Abfrage in die Zwischenablage.|
||---------*Ergebnisse*---------|
|Clear results cache (Ergebniscache löschen)| Löscht zwischengespeicherte Ergebnisse von zuvor ausgeführten Abfragen.| 

## <a name="connections-tab"></a>Registerkarte „Verbindungen“

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Kusto.Explorer: Start":::

|Menü|Verhalten|
|----|----------|
||---------*Gruppen*---------|
|Gruppe hinzufügen| Fügt eine neue Kusto-Servergruppe hinzu.|
|Gruppe umbenennen| Benennt die vorhandene Kusto-Servergruppe um.|
|Gruppe entfernen| Entfernt die vorhandene Kusto-Servergruppe.|
||---------*Cluster*---------|
|Import Connections (Verbindungen importieren)| Importiert Verbindungen aus einer Datei, die Angaben zu Verbindungen enthält.|
|Export Connections (Verbindungen exportieren)| Exportiert Verbindungen in eine Datei.|
|Verbindung hinzufügen| Fügt eine neue Kusto-Serververbindung hinzu.| 
|Verbindung bearbeiten| Öffnet ein Dialogfeld, in dem die Eigenschaften einer Kusto-Serververbindung bearbeitet werden können.|
|Verbindung entfernen| Entfernt die vorhandene Verbindung mit dem Kusto-Server.|
|Aktualisieren| Aktualisiert die Eigenschaften einer Kusto-Serververbindung.|
||---------*Sicherheit*---------|
|Inspect Your ADD Principal (ADD-Prinzipal untersuchen)| Zeigt die Details zum derzeit aktiven Benutzer an.|
|Sign-out From AAD (Von AAD abmelden)| Meldet den aktuellen Benutzer für die Verbindung mit AAD ab.|
||---------*Datenbereich*---------|
|Caching scope (Bereich für Zwischenspeicherung)|<ul><li>Hot Data (Heiße Daten): Abfragen werden nur für den [Cache für heiße Daten](../management/cachepolicy.md) durchgeführt.</li><li>Alle Daten: Abfragen werden für alle verfügbaren Daten durchgeführt (Standardeinstellung).</li></ul> |
|DateTime Column (DateTime-Spalte)| Der Name der Spalte, die für einen Vorfilter für die Zeit verwendet werden kann.|
|Zeitfilter| Wert des Vorfilters für die Zeit.|

## <a name="view-tab"></a>Registerkarte Ansicht

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Kusto.Explorer: Start":::

|Menü|Verhalten|
|----|----------|
||---------*Darstellung*---------|
|Full View Mode (Vollbildmodus) | Maximiert den Arbeitsbereich, indem das Menüband und der Verbindungsbereich ausgeblendet werden.|
|Schriftgrad vergrößern  | Vergrößert den Schriftgrad der Registerkarte „Abfrage“ und des Rasters mit den Ergebnisdaten.|  
|Schriftgrad verkleinern  | Verkleinert den Schriftgrad der Registerkarte „Abfrage“ und des Rasters mit den Ergebnisdaten.|
|Layout zurücksetzen|Setzt das Layout der Andocksteuerelemente und Fenster des Tools zurück.|
|Rename Document Tab (Dokumentregisterkarte umbenennen) |Benennt die ausgewählte Registerkarte um. |
||---------*Datenansicht*---------|
|Ansicht zurücksetzen| Setzt die [Einstellungen der Datenansicht](#dvs) auf die Standardwerte zurück. |
|Explore Column Values (Spaltenwerte untersuchen)|Zeigt die Verteilung der Spaltenwerte an.|
|Focus on Query Statistics (Fokus auf Abfragestatistik)|Legt den Fokus nach Abschluss der Abfrage nicht auf die Abfrageergebnisse, sondern auf die Abfragestatistik fest.|
|Duplikate ausblenden|Aktiviert bzw. deaktiviert die Entfernung der doppelten Zeilen aus den Abfrageergebnissen.|
|Hide Empty Columns (Leere Spalten ausblenden)|Aktiviert bzw. deaktiviert die Entfernung leerer Spalten aus den Abfrageergebnissen.|
|Collapse Singular Columns (Einzelne Spalten reduzieren)|Aktiviert bzw. deaktiviert die Reduzierung von Spalten mit nur einem Wert.|
||---------*Datenfilterung*---------|
|Filter Rows In Search (Zeilen in Suche filtern)|Aktiviert bzw. deaktiviert die Option, bei der in den Abfrageergebnissen einer Suche nur Zeilen mit Übereinstimmungen angezeigt werden (**STRG+F**).|
||---------*Visualisierungen*---------|
|Visualisierungen|Weitere Informationen finden Sie oben unter [Visualisierungen](#visualizations-section). |

> [!NOTE]
> <a id="dvs">Einstellungen für die Datenansicht:</a> 
>
> Mit Kusto.Explorer werden die Einstellungen nachverfolgt, die für einen eindeutigen Satz mit Spalten verwendet werden. Wenn Spalten neu sortiert oder entfernt werden, wird die Datenansicht gespeichert und jeweils wiederverwendet, wenn die Daten mit den gleichen Spalten abgerufen werden. Wählen Sie auf der Registerkarte **Ansicht** die Option **Sicht zurücksetzen** aus, um die Ansicht auf die Standardwerte zurückzusetzen. 

## <a name="tools-tab"></a>Registerkarte „Extras“

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Kusto.Explorer: Start":::

|Menü|Verhalten|
|----|----------|
||---------*IntelliSense*---------|
|IntelliSense aktivieren| Aktiviert bzw. deaktiviert IntelliSense im Skriptbereich.|
||---------*Analysieren*---------|
|Query Analyzer| Startet das Tool „Query Analyzer“.|
|Query Checker (Abfrageüberprüfung) | Analysiert die aktuelle Abfrage und gibt eine Reihe von anwendbaren Verbesserungsempfehlungen aus.|
|Rechner| Startet den Rechner.|
||---------*Analyse*---------|
|Analytical Reports (Analyseberichte)| Öffnet ein Dashboard mit mehreren vordefinierten Berichten für die Datenanalyse.|
||---------*Übersetzen*---------|
|Abfrage an Power BI| Übersetzt eine Abfrage in ein Format, das für die Verwendung in Power BI geeignet ist.|
||---------*Optionen*---------|
|Optionen zurücksetzen| Setzt Anwendungseinstellungen auf die Standardwerte zurück.|
|Optionen| Öffnet ein Tool zum Konfigurieren der Anwendungseinstellungen. Erfahren Sie mehr zu den [Optionen von Kusto.Explorer](kusto-explorer-options.md).|

## <a name="monitoring-tab"></a>Registerkarte „Überwachung“

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Kusto.Explorer: Start":::

|Menü             | Verhalten|
|-----------------|---------| 
||---------*Überwachen*---------|
|Cluster Diagnostics (Clusterdiagnose) | Zeigt eine Integritätszusammenfassung für die Servergruppe an, die im Verbindungsbereich derzeit ausgewählt ist. | 
|Latest data: All tables (Neueste Daten: Alle Tabellen)| Zeigt eine Zusammenfassung der neuesten Daten in allen Tabellen der derzeit ausgewählten Datenbank an.|
|Latest data: Selected table (Neueste Daten: Ausgewählte Tabelle)|Zeigt in der Statusleiste die neuesten Daten der ausgewählten Tabelle an.| 

## <a name="management-tab"></a>Registerkarte „Verwaltung“

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto.Explorer: Start":::

|Menü             | Verhalten|
|-----------------|---------|
||---------*Autorisierte Prinzipale*---------|
|Manage Cluster Authorized Principals (Autorisierte Prinzipale für Cluster verwalten) |Ermöglicht die Verwaltung der Prinzipale eines Clusters für autorisierte Benutzer.| 
|Manage Database Authorized Principals (Autorisierte Prinzipale für Datenbank verwalten) | Ermöglicht die Verwaltung der Prinzipale einer Datenbank für autorisierte Benutzer.| 
|Manage Table Authorized Principals (Autorisierte Prinzipale für Tabelle verwalten) | Ermöglicht die Verwaltung der Prinzipale einer Tabelle für autorisierte Benutzer.| 
|Manage Function Authorized Principals (Autorisierte Prinzipale für Funktion verwalten) | Ermöglicht die Verwaltung der Prinzipale einer Funktion für autorisierte Benutzer.| 

## <a name="help-tab"></a>Registerkarte „Hilfe“

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Kusto.Explorer: Start":::

|Menü             | Verhalten|
|-----------------|---------|
||---------*Dokumentation*---------|
|Hilfe             | Öffnet einen Link zur Kusto-Onlinedokumentation.  | 
|Neues       | Öffnet ein Dokument, in dem alle Änderungen an Kusto.Explorer aufgelistet sind.|
|Report Issue (Problem melden)      |Öffnet ein Dialogfeld mit zwei Optionen: <ul><li>Report issues related to service (Probleme mit Dienst melden)</li><li>Report issues in the client application (Probleme in Clientanwendung melden)</li></ul> | 
|Suggest Feature (Vorschlagfeature)  | Öffnet einen Link zum Kusto-Feedbackforum. | 
|Updates prüfen     | Überprüft, ob Updates für Ihre Version von Kusto.Explorer vorhanden sind. | 

## <a name="connections-panel"></a>Bereich „Verbindungen“

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Kusto.Explorer: Start":::

Im Bereich „Verbindungen“ werden alle konfigurierten Clusterverbindungen angezeigt. Für jeden Cluster werden die darin gespeicherten Datenbanken, Tabellen und Attribute (Spalten) angezeigt. Wählen Sie Einträge aus (um einen impliziten Kontext für die Suche/Abfrage im Hauptbereich festzulegen), oder doppelklicken Sie auf Einträge, um den Namen in den Bereich für die Suche/Abfrage zu kopieren.

Bei einem umfangreichen Schema (z. B. einer Datenbank mit Hunderten von Tabellen) können Sie wie folgt vorgehen: Führen Sie einen Suchvorgang durch, indem Sie **STRG+F** drücken und einen Teil des gesuchten Entitätsnamens eingeben (ohne Berücksichtigung der Groß-/Kleinschreibung).

Kusto.Explorer unterstützt die Steuerung des Verbindungsbereichs über das Abfragefenster. Dies ist für Skripts hilfreich. Beispielsweise können Sie eine Skriptdatei mit einem Befehl starten, mit dem Kusto.Explorer angewiesen wird, eine Verbindung mit dem Cluster bzw. der Datenbank herzustellen, für den bzw. die die Daten mit dem Skript abgefragt werden. Verwenden Sie hierfür die folgende Syntax:

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

Führen Sie die einzelnen Zeilen mit `F5` oder einer entsprechenden Option aus.

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>Steuern der Benutzeridentität für die Verbindungsherstellung mit Kusto.Explorer

Das Standardsicherheitsmodell für neue Verbindungen hat den Typ „AAD-Verbundsicherheit“. Die Authentifizierung erfolgt über Azure Active Directory mit der AAD-Standardbenutzeroberfläche.

Falls Sie die Authentifizierungsparameter genauer steuern müssen, können Sie das Bearbeitungsfeld „Erweitert: Verbindungszeichenfolgen“ erweitern und einen gültigen Wert für die [Kusto-Verbindungszeichenfolge](../api/connection-strings/kusto.md) angeben.

Beispielsweise müssen Benutzer, die auf mehreren AAD-Mandanten vorhanden sind, manchmal eine bestimmte „Projektion“ ihrer Identitäten für einen spezifischen AAD-Mandanten verwenden. Geben Sie zu diesem Zweck eine Verbindungszeichenfolge an, z. B. wie im Beispiel unten (Wörter in Großbuchstaben durch Ihre Werte ersetzen):

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER` ist ein Domänenname oder AAD-Mandant (eine GUID) des AAD-Mandanten, unter dem der Cluster gehostet wird. Hierbei handelt es sich normalerweise um den Domänennamen der Organisation, die den Cluster besitzt, z. B. `contoso.com`. 
* USER_DOMAIN ist die Identität des Benutzers, der für diesen Mandanten eingeladen wurde (z. B. `user@example.com`). 

>[!NOTE]
> Der Domänenname des Benutzers muss nicht unbedingt dem Domänennamen des Mandanten entsprechen, von dem der Cluster gehostet wird.

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Kusto.Explorer: Start":::

## <a name="keyboard-shortcuts"></a>Tastenkombinationen

Unter Umständen stellen Sie fest, dass Sie Vorgänge mit Tastenkombinationen schneller als mit der Maus durchführen können. Sehen Sie sich diese [Liste mit den Tastenkombinationen für Kusto.Explorer](kusto-explorer-shortcuts.md) an, um mehr zu erfahren.

## <a name="table-row-colors"></a>Farben von Tabellenzeilen

Kusto.Explorer versucht, den Schweregrad oder den Ausführlichkeitsgrad der einzelnen Zeilen im Ergebnisbereich zu interpretieren und diese dann entsprechend farbig zu kennzeichnen. Hierfür werden die einzelnen Werte der Spalten anhand von bekannten Mustern abgeglichen („Warnung“, „Fehler“ usw.).

Wählen Sie im Menü **Extras** die Option **Optionen** > **Results Viewer** > **Verbosity color scheme** (Ergebnisansicht > Farbschema für Ausführlichkeit) aus, um das Schema für die Ausgabefarbe zu ändern oder dieses Verhalten zu deaktivieren.

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Kusto.Explorer: Start":::

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über die Verwendung von Kusto.Explorer:

* [Verwenden von Kusto.Explorer](kusto-explorer-using.md)
* [Tastenkombinationen für Kusto.Explorer](kusto-explorer-shortcuts.md)
* [Optionen von Kusto.Explorer](kusto-explorer-options.md)
* [Problembehandlung für Kusto.Explorer](kusto-explorer-troubleshooting.md)

Informieren Sie sich über die Tools und Hilfsprogramme von Kusto.Explorer:
* [Codeanalyse von Kusto.Explorer](kusto-explorer-code-analyzer.md)
* [Codenavigation in Kusto.Explorer](kusto-explorer-codenav.md)
* [Code-Refactoring in Kusto.Explorer](kusto-explorer-refactor.md)
* [Kusto-Abfragesprache (KQL)](/azure/kusto/query/)