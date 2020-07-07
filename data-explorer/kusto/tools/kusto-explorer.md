---
title: Getting Started with Kusto. Explorer
description: Informieren Sie sich über die Features von Kusto. Explorer und darüber, wie Sie Ihre Daten untersuchen können.
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: 6c36ace478e9bc026c028bfb6393bcfdf4eebdfa
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013927"
---
# <a name="getting-started-with-kustoexplorer"></a>Getting Started with Kusto. Explorer

Kusto. Explorer ist eine umfangreiche Desktop Anwendung, mit der Sie Ihre Daten mithilfe der Kusto-Abfragesprache in einer benutzerfreundlichen Benutzeroberfläche untersuchen können. In dieser Übersicht werden die ersten Schritte beim Einrichten von Kusto. Explorer erläutert und die Benutzeroberfläche erläutert, die Sie verwenden werden.

Mit Kusto. Explorer können Sie folgende Aktionen ausführen:
* [Fragen Sie Ihre Daten](kusto-explorer-using.md#query-mode)ab.
* Durch [Suchen Sie Ihre Daten](kusto-explorer-using.md#search-mode) in Tabellen.
* [Visualisieren Sie Ihre Daten](#visualizations-section) in einer Vielzahl von Diagrammen.
* [Freigeben von Abfragen und Ergebnissen](kusto-explorer-using.md#share-queries-and-results) per e-Mail oder Verwenden von Deep-Links.

## <a name="installing-kustoexplorer"></a>Installieren von Kusto. Explorer

* Installieren Sie das [Tool Kusto. Explorer](https://aka.ms/ke).

* Sie können mit Ihrem Browser unter auf Ihren Kusto-Cluster zugreifen `https://<your_cluster>.kusto.windows.net` . (Ersetzen `<your_cluster>` Sie durch den Namen Ihres Azure-Daten-Explorer Clusters.)

### <a name="using-chrome-and-kustoexplorer"></a>Verwenden von Chrome und Kusto. Explorer

Wenn Sie Chrome als Standardbrowser verwenden, stellen Sie sicher, dass Sie die ClickOnce-Erweiterung für Chrome installieren:

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)

## <a name="overview-of-the-user-interface"></a>Übersicht über die Benutzeroberfläche

Die Benutzeroberfläche "Kusto. Explorer" wurde mit einem Layout entworfen, das auf Registerkarten und Panels basiert, ähnlich wie bei anderen Microsoft-Produkten: 

1. Navigieren Sie im [Menü](#menu-panel) Bereich durch die Registerkarten, um verschiedene Vorgänge auszuführen.
2. Verwalten von Verbindungen im Bereich " [Verbindungen](#connections-panel) "
3. Erstellen von Skripts, die im Skript Panel ausgeführt werden sollen
4. Anzeigen der Ergebnisse der Skripts im Ergebnisbereich

:::image type="content" source="images/kusto-explorer/ke-start.png" alt-text="Kusto-Explorer starten":::

## <a name="menu-panel"></a>Menübereich

Der Menübereich von Kusto. Explorer umfasst die folgenden Registerkarten:

* [Home](#home-tab)
* [Datei](#file-tab)
* [Verbindungen](#connections-tab)
* [Ansicht](#view-tab)
* [Extras](#tools-tab)
* [Überwachung](#monitoring-tab)
* [Verwaltung](#management-tab)
* [Hilfe](#help-tab)

### <a name="home-tab"></a>Registerkarte "Home"

:::image type="content" source="images/kusto-explorer/home-tab.png" alt-text="Registerkarte "Kusto Explorer Home"":::

Auf der Registerkarte Home werden die zuletzt verwendeten Funktionen angezeigt, die in Abschnitte unterteilt sind:

* [Abfrage](#query-section)
* [Freigeben](#share-section)
* [Visualisierungen](#visualizations-section)
* [Ansicht](#view-section)
* [Hilfe](#help-tab) 

### <a name="query-section"></a>Abschnitt "Abfrage"

:::image type="content" source="images/kusto-explorer/home-query-menu.png" alt-text="Menü "Abfrage" Kusto-Explorer":::

|Menü|    Verhalten|
|----|----------|
|Dropdown Menü für Modus | <ul><li>Abfrage Modus: schaltet das Abfragefenster in einen [Skript Modus](kusto-explorer-using.md#query-mode)um. Befehle können geladen und als Skripts gespeichert werden (Standard).</li> <li> Suchmodus: ein einzelner Abfrage Modus, in dem jeder eingegebene Befehl sofort verarbeitet wird und ein Ergebnis im Ergebnisfenster anzeigt.</li> <li>Search + +-Modus: ermöglicht die Suche nach einem Begriff mithilfe der Such Syntax in einer oder mehreren Tabellen. Weitere Informationen zum Verwenden des [Such Modus + +](kusto-explorer-using.md#search-mode)</li></ul> |
|Neue Registerkarte| Öffnet eine neue Registerkarte zum Abfragen von Kusto. |

### <a name="share-section"></a>Freigabe Abschnitt

:::image type="content" source="images/kusto-explorer/home-share-menu.png" alt-text="Menü für Kusto-Explorer-Freigabe":::

|Menü|    Verhalten|
|----|----------|
|Daten in Zwischenablage|    Exportiert Abfragen und Datasets in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Ergebnis in Zwischenablage| Exportiert das Dataset in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Abfrage in Zwischenablage| Exportiert die Abfrage in eine Zwischenablage.|

### <a name="visualizations-section"></a>Abschnitt "Visualisierungen"

:::image type="content" source="images/kusto-explorer/home-visualizations-menu.png" alt-text="Menü Visualisierungen im Kusto-Explorer":::

|Menü         | Verhalten|
|-------------|---------|
|Flächendiagramm      | Zeigt ein Flächen Diagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet. |
|Säulendiagramm | Zeigt ein Säulendiagramm an, in dem alle numerischen Spalten verschiedenen Reihen (Y-Achse) zugeordnet sind. Die Text Spalte vor der numerischen ist die X-Achse (kann in der Benutzeroberfläche gesteuert werden).|
|Balkendiagramm    | Zeigt ein Balkendiagramm an, in dem alle numerischen Spalten verschiedenen Reihen (X-Achse) zugeordnet sind. Die Text Spalte vor der numerischen ist die Y-Achse (kann in der Benutzeroberfläche gesteuert werden).|
|Gestapeltes Flächendiagramm      | Zeigt ein gestapeltes Flächen Diagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet. |
|Zeitachsendiagramm   | Zeigt ein Zeitdiagramm an, in dem die X-Achse die erste Spalte ist (muss DateTime sein). Alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet.|
|Liniendiagramm   | Zeigt ein Liniendiagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet.|
|Anomaliediagramm|    Ähnlich wie bei timechart, findet aber Anomalien in Zeitreihendaten mithilfe des Algorithmus für die Machine Learning-Anomalien. Zur Anomalieerkennung verwendet Kusto. Explorer die [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) -Funktion. (*) 
|Kreisdiagramm    |    Zeigt ein Kreis Diagramm an, in dem die farbachse die erste Spalte ist. Die zweite Spalte ist die-Achse (muss ein Measure sein, die in Prozent konvertiert wurde).|
|Zeit Leiter |    Zeigt ein Leitungs Diagramm an, in dem die X-Achse die letzten zwei Spalten ist (muss DateTime lauten). Die Y-Achse ist ein zusammengesetzter der anderen Spalten.|
|Punktdiagramm| Zeigt ein Punkt Diagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein). Alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet.|
|PivotChart  | Zeigt eine Pivottabelle und ein PivotChart an, das die vollständige Flexibilität beim Auswählen von Daten, Spalten, Zeilen und verschiedenen Diagrammtypen ermöglicht.| 
|Zeit Pivot   | Interaktive Navigation über die Ereignis Zeitachse (Pivotierung auf der Zeitachse)|

(*) Anomaliediagramm: der Algorithmus erwartet Zeitreihe-Daten, die aus zwei Spalten bestehen:
1. Zeit in festem Intervall
2. Numerischer Wert für die Anomalieerkennung, um Zeitreihe-Daten in Kusto. Explorer zusammenzufassen, zusammenfassen nach dem Uhrzeitfeld und angeben des Zeit Bucket Behälters.

### <a name="view-section"></a>Abschnitt anzeigen

:::image type="content" source="images/kusto-explorer/home-view-menu.png" alt-text="Menü der Kusto-Explorer-Ansicht":::

|Menü           | Verhalten|
|---------------|---------|
|Vollständiger Ansichtsmodus | Maximiert den Arbeitsbereich, indem das Menü Band Menü und der Verbindungs Panel ausgeblendet werden. Beenden Sie den **vollständigen**Ansichtsmodus, indem Sie den Modus für die  >  **vollständige Anzeige**auswählen oder **F11**drücken.|
|Leere Spalten ausblenden| Entfernt leere Spalten aus dem Datenraster.|
|Einzelne Spalten zuklappen| Reduziert Spalten mit Singular Werten.|
|Untersuchen von Spaltenwerten| Zeigt die Verteilung von Spaltenwerten an|
|Schriftart vergrößern  | Vergrößert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|  
|Schriftart verkleinern  | Verringert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|

(*) Daten Ansichts Einstellungen: Kusto. Explorer verfolgt, welche Einstellungen pro eindeutigem Satz von Spalten verwendet werden. Wenn Spalten neu angeordnet oder entfernt werden, wird die Daten Sicht gespeichert und immer dann wieder verwendet, wenn die Daten mit denselben Spalten abgerufen werden. Um die Ansicht auf die Standardwerte zurückzusetzen, wählen Sie auf der Registerkarte **Ansicht** die Option **Ansicht zurücksetzen**aus. 

## <a name="file-tab"></a>Registerkarte „Datei“

:::image type="content" source="images/kusto-explorer/file-tab.png" alt-text="Registerkarte "Kusto-Explorer"":::

|Menü| Verhalten|
|---------------|---------|
||---------*Abfrage Skript*---------|
|Neue Registerkarte | Öffnet ein neues Registerkarten Fenster zum Abfragen von Kusto. |
|Datei öffnen| Lädt Daten aus einer *. kql-Datei in den aktiven Skript Bereich.|
|In Datei speichern| Speichert den Inhalt des aktiven Skript Panels in der *. kql-Datei.|
|Registerkarte schließen| Schließt das aktuelle Registerkarten Fenster.|
||---------*Daten speichern*---------|
|Daten in CSV       | Exportiert Daten in eine CSV-Datei (durch Kommas getrennte Werte).| 
|Daten zu JSON      | Exportiert Daten in eine JSON-formatierte Datei.|
|Daten nach Excel     | Exportiert Daten in eine XLSX-Datei (Excel).|
|Daten in Text      | Exportiert Daten in eine txt-Datei (Text).| 
|Skript für Daten zu kql| Exportiert eine Abfrage in eine Skriptdatei.| 
|Daten zu Ergebnissen   | Exportiert Abfragen und Daten in eine Ergebnisdatei (qres).|
|Abfrage in CSV ausführen |Führt eine Abfrage aus und speichert die Ergebnisse in einer lokalen CSV-Datei.|
||---------*Laden von Daten*---------|
|Aus Ergebnissen|    Lädt Abfragen und Daten aus einer Ergebnisdatei (qres).| 
||---------*Clip*---------|
|Abfragen und Ergebnisse in die Zwischenablage|    Exportiert Abfragen und Datasets in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Ergebnis in Zwischenablage| Exportiert Datasets in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Abfrage in Zwischenablage| Exportiert die Abfrage in eine Zwischenablage.|
||---------*Folgen*---------|
|Ergebnis Cache löschen| Löscht zwischengespeicherte Ergebnisse von zuvor ausgeführten Abfragen.| 

## <a name="connections-tab"></a>Registerkarte „Verbindungen“

:::image type="content" source="images/kusto-explorer/connections-tab.png" alt-text="Registerkarte "Kusto Explorer Connections"":::

|Menü|Verhalten|
|----|----------|
||---------*Musik*---------|
|Gruppe hinzufügen| Fügt eine neue Kusto-Server Gruppe hinzu.|
|Gruppe umbenennen| Benennt die vorhandene Kusto-Server Gruppe um.|
|Gruppe entfernen| Entfernt die vorhandene Kusto-Server Gruppe.|
||---------*Super*---------|
|Importieren von Verbindungen| Importiert Verbindungen aus einer Datei, die Verbindungen angibt.|
|Verbindungen exportieren| Exportiert Verbindungen in eine Datei.|
|Verbindung hinzufügen| Fügt eine neue Kusto-Server Verbindung hinzu.| 
|Verbindung bearbeiten| Öffnet ein Dialogfeld für die Verbindungs Eigenschaften der Kusto-Server Verbindung.|
|Verbindung entfernen| Entfernt die vorhandene Verbindung mit dem Kusto-Server.|
|Aktualisieren| Aktualisiert die Eigenschaften einer Kusto-Server Verbindung.|
||---------*Sicherung*---------|
|Überprüfen des hinzufügen| Zeigt die aktiven Benutzer Details der Ströme an|
|Abmelden von Aad| Meldet den aktuellen Benutzer von der Verbindung mit Aad ab.|
||---------*Datenbereich*---------|
|Cache Bereich|<ul><li>Hot dataexecute-Abfragen nur für den [Hot-Data-Cache](../management/cachepolicy.md)</li><li>Alle Daten: Ausführen von Abfragen für alle verfügbaren Daten (Standard)</li></ul> |
|Datetime-Spalte| Der Name der Spalte, die für einen Zeit präfilter verwendet werden kann.|
|Zeit Filter| Wert der Zeit vor dem Filtern|

## <a name="view-tab"></a>Registerkarte Ansicht

:::image type="content" source="images/kusto-explorer/view-tab.png" alt-text="Registerkarte "Kusto-Explorer"":::

|Menü|Verhalten|
|----|----------|
||---------*Auftritts*---------|
|Vollständiger Ansichtsmodus | Maximiert den Arbeitsbereich, indem das Menü Band Menü und der Verbindungs Panel ausgeblendet werden.|
|Schriftart vergrößern  | Vergrößert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|  
|Schriftart verkleinern  | Verringert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|
|Layout zurücksetzen|Setzt das Layout der Andock Steuerelemente und Fenster des Tools zurück.|
|Registerkarte Dokument umbenennen |Ausgewählte Registerkarte umbenennen |
||---------*Datenansicht*---------|
|Ansicht zurücksetzen| Setzt die Daten Ansichts Einstellungen auf die Standardwerte zurück (*).|
|Untersuchen von Spaltenwerten|Zeigt die Verteilung von Spaltenwerten an|
|Fokus auf Abfrage Statistik|Ändert den Fokus auf Abfrage Statistik anstelle von Abfrage Ergebnissen nach Abschluss der Abfrage.|
|Duplikate ausblenden|Schaltet die Entfernung der doppelten Zeilen aus den Abfrage Ergebnissen um.|
|Leere Spalten ausblenden|Schaltet das Entfernen leerer Spalten aus den Abfrage Ergebnissen um.|
|Einzelne Spalten zuklappen|Schaltet reduzierende Spalten mit einem Singular Wert um.|
||---------*Datenfilterung*---------|
|Filtern von Zeilen in der Suche|Schaltet die Option zum Anzeigen von nur übereinstimmenden Zeilen in der Abfrageergebnis Suche um (**STRG + F**).|
||---------*Visualisierungen*---------|
|Visualisierungen|Siehe [Visualisierungen](#visualizations-section)weiter oben. |

(*) Daten Ansichts Einstellungen: in Kusto. Explorer werden die Einstellungen nachverfolgt, die pro eindeutigem Satz von Spalten verwendet werden. Wenn Spalten neu angeordnet oder entfernt werden, wird die Datenansicht gespeichert und immer dann wieder verwendet, wenn die Daten mit denselben Spalten abgerufen werden. Um die Ansicht auf die Standardwerte zurückzusetzen, wählen Sie auf der Registerkarte **Ansicht** die Option **Ansicht zurücksetzen**aus. 

## <a name="tools-tab"></a>Registerkarte „Extras“

:::image type="content" source="images/kusto-explorer/tools-tab.png" alt-text="Registerkarte "Kusto Explorer Tools"":::

|Menü|Verhalten|
|----|----------|
||---------*IntelliSense*---------|
|IntelliSense aktivieren| Aktiviert und deaktiviert IntelliSense im Skript Panel.|
||---------*Analy*---------|
|Query Analyzer| Hiermit wird das Abfrage Analysetool gestartet.|
|Abfrage Prüfung | Analysiert die aktuelle Abfrage und gibt eine Reihe von anwendbaren Verbesserungsempfehlungen aus.|
|Rechner| Starten des Rechners|
||---------*Analytik*---------|
|Analytische Berichte| Öffnet ein Dashboard mit mehreren vorgefertigten Berichten für die Datenanalyse.|
||---------*Übersetzen*---------|
|Abfrage an Power BI| Übersetzt eine Abfrage in ein Format, das für die Verwendung von in geeignet ist Power BI|
||---------*Optionen*---------|
|Optionen zurücksetzen| Legt die Anwendungseinstellungen auf die Standardwerte fest.|
|Optionen| Öffnet ein Tool zum Konfigurieren von Anwendungseinstellungen. Weitere Informationen zu [Kusto. Explorer-Optionen](kusto-explorer-options.md).|

## <a name="monitoring-tab"></a>Registerkarte Überwachung

:::image type="content" source="images/kusto-explorer/monitoring-tab.png" alt-text="Registerkarte "Kusto Explorer Monitoring"":::

|Menü             | Verhalten|
|-----------------|---------| 
||---------*Über*---------|
|Cluster Diagnose | Zeigt eine Integritäts Zusammenfassung für die derzeit im Verbindungs Panel ausgewählte Server Gruppe an. | 
|Neueste Daten: alle Tabellen| Zeigt eine Zusammenfassung der neuesten Daten in allen Tabellen der aktuell ausgewählten Datenbank an.|
|Neueste Daten: ausgewählte Tabelle|Zeigt in der Statusleiste die neuesten Daten in der ausgewählten Tabelle an.| 

## <a name="management-tab"></a>Verwaltungs Registerkarte

:::image type="content" source="images/kusto-explorer/management-tab.png" alt-text="Kusto-Explorer-Verwaltungs Registerkarte":::

|Menü             | Verhalten|
|-----------------|---------|
||---------*Autorisierte Prinzipale*---------|
|Verwalten von autorisierten clusterprinzipale |Ermöglicht das Verwalten der Prinzipale eines Clusters für autorisierte Benutzer.| 
|Autorisierte autorisierte Daten Bank Prinzipale verwalten | Ermöglicht das Verwalten der Prinzipale einer Datenbank für autorisierte Benutzer.| 
|Verwalten von autorisierten Tabellen Prinzipale | Ermöglicht das Verwalten der Prinzipale einer Tabelle für autorisierte Benutzer.| 
|Verwalten von autorisierten Funktions Prinzipale | Ermöglicht das Verwalten der Prinzipale einer Funktion für autorisierte Benutzer.| 

## <a name="help-tab"></a>Hilfe Registerkarte

:::image type="content" source="images/kusto-explorer/help-tab.png" alt-text="Hilfe Registerkarte für Kusto-Explorer":::

|Menü             | Verhalten|
|-----------------|---------|
||---------*Dokumentation*---------|
|Hilfe             | Öffnet einen Link zur Kusto-Online Dokumentation.  | 
|Neuigkeiten       | Öffnet ein Dokument, in dem alle Änderungen an Kusto. Explorer aufgelistet sind.|
|Report Issue (Problem melden)      |Öffnet ein Dialogfeld mit zwei Optionen: <ul><li>Probleme im Zusammenhang mit dem Dienst melden</li><li>Melden von Problemen in der Client Anwendung</li></ul> | 
|Feature vorschlagen  | Öffnet einen Link zum Kusto-Feedback Forum. | 
|Updates überprüfen     | Prüft, ob Updates für Ihre Version von Kusto. Explorer vorhanden sind. | 

## <a name="connections-panel"></a>Verbindungs Panel

:::image type="content" source="images/kusto-explorer/connections-panel.png" alt-text="Bereich für Kusto-Explorer-Verbindungen":::

Im Bereich Verbindungen werden alle konfigurierten Cluster Verbindungen angezeigt. Für jeden Cluster werden die Datenbanken, Tabellen und Attribute (Spalten) angezeigt, die Sie speichern. Wählen Sie Elemente aus (wodurch ein impliziter Kontext für die Suche/Abfrage im Hauptbereich festgelegt wird), oder Doppelklicken Sie auf Elemente, um den Namen in das Such-/abfragenpanel zu kopieren.

Wenn das tatsächliche Schema groß ist (z. b. eine Datenbank mit Hunderten von Tabellen), können Sie es durchsuchen, indem Sie **STRG + F** drücken und eine Teil Zeichenfolge (ohne Beachtung der Groß-/Kleinschreibung) des gesuchten Entitäts Namens eingeben.

Kusto. Explorer unterstützt das Steuern des Verbindungs Bereichs über das Abfragefenster, was für Skripts nützlich ist. Beispielsweise können Sie eine Skriptdatei mit einem Befehl starten, der Kusto. Explorer anweist, eine Verbindung mit dem Cluster bzw. der Datenbank herzustellen, deren Daten durch das Skript abgefragt werden, indem Sie die folgende Syntax verwenden:

<!-- csl -->
```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

Führen Sie jede Zeile mit `F5` oder ähnlich aus.

### <a name="control-the-user-identity-connecting-to-kustoexplorer"></a>Steuern der Benutzeridentität, die eine Verbindung zu Kusto. Explorer herstellt

Das Standard Sicherheitsmodell für neue Verbindungen ist die Sicherheit mit Aad-Verbund. Die Authentifizierung erfolgt über die Azure Active Directory mithilfe der standardmäßigen Aad-Benutzerfunktion.

Wenn Sie eine präzisere Steuerung der Authentifizierungs Parameter benötigen, können Sie das Bearbeitungsfeld "Erweiterte Verbindungs Zeichenfolgen" erweitern und einen gültigen [Kusto-Verbindungs Zeichenfolgen](../api/connection-strings/kusto.md) -Wert angeben.

Beispielsweise müssen Benutzer, die in mehreren Aad-Mandanten vorhanden sind, manchmal eine bestimmte "Projektion" ihrer Identitäten für einen bestimmten Aad-Mandanten verwenden. Geben Sie hierzu eine Verbindungs Zeichenfolge an, z. b. die folgende (ersetzen Sie Wörter in Großbuchstaben durch bestimmte Werte):

```kusto
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

* `AAD_TENANT_OF_CLUSTER`ist ein Domänen Name oder eine Aad-Mandanten-ID (eine GUID) des Aad-Mandanten, in dem der Cluster gehostet wird. Dabei handelt es sich normalerweise um den Domänen Namen der Organisation, die den Cluster besitzt, z `contoso.com` . b.. 
* USER_DOMAIN ist die Identität des Benutzers, der in diesen Mandanten geladen wurde (z `user@example.com` . b.). 

>[!Note]
> Der Domänen Name des Benutzers ist nicht notwendigerweise identisch mit dem des Mandanten, der den Cluster gehostet.

:::image type="content" source="images/kusto-explorer/advanced-connection-string.png" alt-text="Erweiterte Verbindungs Zeichenfolge für Kusto-Explorer":::

## <a name="keyboard-shortcuts"></a>Tastenkombinationen

Sie können feststellen, dass Sie mithilfe von Tastenkombinationen Vorgänge schneller ausführen können als mit der Maus. Weitere Informationen finden Sie in [der Liste der Tastenkombinationen für Kusto. Explorer](kusto-explorer-shortcuts.md) .

## <a name="table-row-colors"></a>Tabellenzeilen Farben

Der Schweregrad oder der ausführlichkeits Grad der einzelnen Zeilen im Ergebnisbereich wird von Kusto. Explorer interpretiert und entsprechend farbig angezeigt. Dies geschieht, indem die unterschiedlichen Werte der einzelnen Spalten mit einem Satz bekannter Muster ("Warnung", "Fehler" usw.) abgeglichen werden.

Um das Ausgabe Farbschema zu ändern oder dieses Verhalten zu deaktivieren, wählen Sie **im Menü Extras** die **Option Optionen**  >  **Ergebnis-Viewer**ausführlichkeits  >  **Farbschema**aus.

:::image type="content" source="images/kusto-explorer/ke-color-scheme.png" alt-text="Änderung des Farbschemas des Kusto-Explorers":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit "Kusto. Explorer" finden Sie hier:

* [Verwenden von Kusto.Explorer](kusto-explorer-using.md)
* [Kusto. Explorer-Tastenkombinationen](kusto-explorer-shortcuts.md)
* [Optionen von Kusto.Explorer](kusto-explorer-options.md)
* [Problembehandlung für Kusto.Explorer](kusto-explorer-troubleshooting.md)

Erfahren Sie mehr über die Tools und Hilfsprogramme von Kusto. Explorer:
* [Kusto. Explorer-Code Analyse](kusto-explorer-code-analyzer.md)
* [Code Navigation in Kusto. Explorer](kusto-explorer-codenav.md)
* [Refactoring von Kusto. Explorer-Code](kusto-explorer-refactor.md)
* [Kusto-Abfragesprache (kql)](https://docs.microsoft.com/azure/kusto/query/)
