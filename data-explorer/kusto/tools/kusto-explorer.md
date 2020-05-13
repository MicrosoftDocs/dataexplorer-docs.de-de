---
title: 'Kusto. Explorer-Tool: Azure Daten-Explorer'
description: In diesem Artikel wird das Tool Kusto. Explorer in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 60a414ff871d88de041e8b76671b73d98854fba0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374073"
---
# <a name="kustoexplorer-tool"></a>Kusto. Explorer-Tool

Kusto. Explorer ist eine umfangreiche Desktop Anwendung, mit der Sie Ihre Daten mithilfe der Kusto-Abfragesprache untersuchen können.

## <a name="getting-the-tool"></a>Holen Sie sich das Tool

* Installieren des [Kusto. Explorer-Tools](https://aka.ms/Kusto.Explorer)

* Sie können auch mit Ihrem Browser auf Ihren Kusto-Cluster zugreifen unter: `https://<your_cluster>.kusto.windows.net` . Ersetzen Sie <your_cluster> durch den Namen Ihres Azure-Daten-Explorer Clusters.



## <a name="using-chrome-and-kustoexplorer"></a>Verwenden von Chrome und Kusto. Explorer

Wenn Sie Chrome als Standardbrowser verwenden, stellen Sie sicher, dass Sie die ClickOnce-Erweiterung für Chrome installieren:

[https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US](https://chrome.google.com/webstore/detail/clickonce-for-google-chro/kekahkplibinaibelipdcikofmedafmb/related?hl=en-US)



## <a name="overview-of-the-user-experience"></a>Übersicht über die Benutzer Darstellung

Das Kusto-Explorer-Fenster verfügt über mehrere Benutzeroberflächen Komponenten:

1. [Menübereich](#menu-panel)
2. [Verbindungs Panel](#connections-panel)
3. Skript Panel
4. Bereich "Ergebnisse"

![Starten von Kusto. Explorer](./Images/KustoTools-KustoExplorer/ke-start.png "KE-Start")

### <a name="keyboard-shortcuts"></a>Tastenkombinationen

Sie können feststellen, dass Sie mithilfe von Tastenkombinationen Vorgänge schneller ausführen können als mit der Maus. Sehen Sie sich diese [Liste der Tastenkombinationen für "Kusto. Explorer](kusto-explorer-shortcuts.md)" an.

### <a name="menu-panel"></a>Menübereich

Der Menübereich von Kusto. Explorer umfasst die folgenden Registerkarten:

* [Start](#home-tab)
* [File](#file-tab)
* [Verbindungen](#connections-tab)
* [Ansicht](#view-tab)
* [Tools](#tools-tab)

* [Verwaltung](#management-tab)
* [Hilfe](#help-tab)

### <a name="home-tab"></a>Registerkarte "Home"

![Kusto. Explorer-Startseite](./Images/KustoTools-KustoExplorer/home-tab.png "Registerkarte „Start“")

Auf der Registerkarte Home werden die zuletzt verwendeten Funktionen angezeigt, die in Abschnitte unterteilt sind:

* [Abfrage](#query-section)
* [Teilen](#share-section)
* [Visualisierungen](#visualizations-section)
* [Ansicht](#view-section)
* [Hilfe](#help-tab) 

#### <a name="query-section"></a>Abschnitt "Abfrage"

![Kusto. Explorer-Abfrage Menü](./Images/KustoTools-KustoExplorer/home-query-menu.png "Abfrage-Menü")

|Menü|    Verhalten|
|----|----------|
|Dropdown Menü für Modus | <ul><li>Abfrage Modus: schaltet das Abfragefenster in einen [Skript Modus](#query-mode)um. Befehle können geladen und als Skripts gespeichert werden (Standard).</li> <li> Suchmodus: ein einzelner Abfrage Modus, in dem jeder eingegebene Befehl sofort verarbeitet wird und ein Ergebnis im Ergebnisfenster anzeigt.</li> <li>Search + +-Modus: ermöglicht die Suche nach einem Begriff mithilfe der Such Syntax in einer oder mehreren Tabellen. Weitere Informationen zum Verwenden des [Such Modus + +](kusto-explorer.md#search-mode)</li></ul> |
|Neue Registerkarte| Öffnet eine neue Registerkarte zum Abfragen von Kusto. |

#### <a name="share-section"></a>Freigabe Abschnitt

!["Kusto. Explorer"-Freigabe Abschnitt](./Images/KustoTools-KustoExplorer/home-share-menu.png "Freigabe-Menü")

|Menü|    Verhalten|
|----|----------|
|Daten in Zwischenablage|    Exportiert Abfragen und Datasets in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Ergebnis in Zwischenablage| Exportiert das Dataset in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Abfrage in Zwischenablage| Exportiert die Abfrage in eine Zwischenablage.|

#### <a name="visualizations-section"></a>Abschnitt "Visualisierungen"

![alt text](./Images/KustoTools-KustoExplorer/home-visualizations-menu.png "Menü-Visualisierungen")

|Menü         | Verhalten|
|-------------|---------|
|Flächendiagramm      | Zeigt ein Flächen Diagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein), und alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet. |
|Säulendiagramm | Zeigt ein Säulendiagramm an, in dem alle numerischen Spalten verschiedenen Reihen (Y-Achse) zugeordnet sind, und die Text Spalte vor dem numerischen Wert die X-Achse (kann in der Benutzeroberfläche gesteuert werden).|
|Balkendiagramm    | Zeigt ein Balkendiagramm an, in dem alle numerischen Spalten verschiedenen Reihen (X-Achse) zugeordnet sind, und die Text Spalte vor der numerischen Achse die Y-Achse (kann in der Benutzeroberfläche gesteuert werden).|
|Gestapeltes Flächendiagramm      | Zeigt ein gestapeltes Flächen Diagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein), und alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet. |
|Zeitachsendiagramm   | Zeigt ein Zeitdiagramm an, in dem die X-Achse die erste Spalte ist (muss DateTime sein), und alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet.|
|Liniendiagramm   | Zeigt ein Liniendiagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein), und alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet.|
|Anomaliediagramm|    Ähnlich wie bei timechart, findet aber Anomalien in Zeitreihendaten mithilfe des Algorithmus für die Machine Learning-Anomalien. Zur Anomalieerkennung verwendet Kusto. Explorer die [series_decompose_anomalies](../query/series-decompose-anomaliesfunction.md) -Funktion. (*) 
|Kreisdiagramm    |    Zeigt ein Kreis Diagramm an, in dem die farbachse die erste Spalte ist, und die-Achse (muss ein Measure sein, das in Prozent konvertiert wird) ist die zweite Spalte.|
|Leitungs Diagramm |    Zeigt ein Leitungs Diagramm an, in dem die X-Achse die letzten zwei Spalten ist (muss DateTime lauten) und die Y-Achse ein zusammengesetzter der anderen Spalten ist.|
|Punktdiagramm| Zeigt ein Punkt Diagramm an, in dem die X-Achse die erste Spalte ist (muss numerisch sein), und alle numerischen Spalten werden verschiedenen Reihen (Y-Achse) zugeordnet.|
|PivotChart  | Zeigt eine Pivottabelle und ein PivotChart an, das die vollständige Flexibilität beim Auswählen von Daten, Spalten, Zeilen und verschiedenen Diagrammtypen ermöglicht.| 
|Zeit Pivot   | Interaktive Navigation über die Ereignis Zeitachse (Pivotierung auf der Zeitachse)|

(*) Anomaliediagramm: der Algorithmus erwartet Zeitreihe-Daten, die aus zwei Spalten bestehen:
1. Zeit in festem Intervall
2. Numerischer Wert für die Anomalieerkennung, um dies in Kusto. Explorer zu erzielen, zusammengefasst nach dem Zeitfeld und angeben des Zeit Bucket Behälters.

#### <a name="view-section"></a>Abschnitt anzeigen

![alt text](./Images/KustoTools-KustoExplorer/home-view-menu.png "Menü "Ansicht"")

|Menü           | Verhalten|
|---------------|---------|
|Vollständiger Ansichtsmodus | Maximiert den Arbeitsbereich, indem das Menü Band Menü und der Verbindungs Panel ausgeblendet werden.|
|Leere Spalten ausblenden| Entfernt leere Spalten aus dem Datenraster.|
|Einzelne Spalten zuklappen| Reduziert Spalten mit Singular Werten.|
|Untersuchen von Spaltenwerten| Zeigt die Verteilung von Spaltenwerten an|
|Schriftart vergrößern  | Vergrößert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|  
|Schriftart verkleinern  | Verringert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|

(*) Daten Ansichts Einstellungen: Kusto. Explorer verfolgt, welche Einstellungen pro eindeutigem Satz von Spalten verwendet werden. Wenn Spalten neu angeordnet oder entfernt werden, wird die Daten Sicht gespeichert und immer dann wieder verwendet, wenn die Daten mit denselben Spalten abgerufen werden. Um die Ansicht auf die Standardwerte zurückzusetzen, wählen Sie auf der Registerkarte **Ansicht** die Option **Ansicht zurücksetzen**aus. 

### <a name="file-tab"></a>Registerkarte „Datei“

![Datei "Kusto. Explorer"](./Images/KustoTools-KustoExplorer/file-tab.png "Registerkarte "Datei"")

|Menü| Verhalten|
|---------------|---------|
||---------*Abfrage Skript*---------|
|Neue Registerkarte | Öffnet ein neues Registerkarten Fenster zum Abfragen von Kusto. |
|Datei öffnen| Lädt Daten aus einer *. kql-Datei in den aktiven Skript Bereich.|
|In Datei speichern| Speichert den Inhalt des aktiven Skript Panels in der *. kql-Datei.|
|Registerkarte schließen| Schließt das aktuelle Registerkarten Fenster.|
||---------*Daten speichern*---------|
|An CSV       | Exportiert Daten in eine CSV-Datei (durch Kommas getrennte Werte).| 
|In JSON      | Exportiert Daten in eine JSON-formatierte Datei.|
|In Excel     | Exportiert Daten in eine XLSX-Datei (Excel).|
|An Text      |    Exportiert Daten in eine txt-Datei (Text).| 
|Zum CSL-Skript|    Exportiert eine Abfrage in eine Skriptdatei.| 
|Zu Ergebnissen   |    Exportiert Abfragen und Daten in eine Ergebnisdatei (qres).|
||---------*Laden von Daten*---------|
|Aus Ergebnissen|    Lädt Abfragen und Daten aus einer Ergebnisdatei (qres).| 
||---------*Clip*---------|
|Daten in Zwischenablage|    Exportiert Abfragen und Datasets in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Ergebnis in Zwischenablage| Exportiert Datasets in eine Zwischenablage. Wenn ein Diagramm angezeigt wird, wird das Diagramm als Bitmap exportiert.| 
|Abfrage in Zwischenablage| Exportiert die Abfrage in die Zwischenablage.|
||---------*Folgen*---------|
|Ergebnis Cache löschen| Löscht zwischengespeicherte Ergebnisse von zuvor ausgeführten Abfragen.| 

### <a name="connections-tab"></a>Registerkarte Verbindungen

![Registerkarte "Kusto. Explorer-Verbindungen"](./Images/KustoTools-KustoExplorer/connections-tab.png "Registerkarte "Verbindungen"")

|Menü|Verhalten|
|----|----------|
||---------*Musik*---------|
|Gruppe hinzufügen| Fügt eine neue Kusto-Server Gruppe hinzu.|
|Gruppe umbenennen| Benennt die vorhandene Kusto-Server Gruppe um.|
|Gruppe entfernen| Entfernt die vorhandene Kusto-Server Gruppe.|
||---------*Super*---------|
|Importieren von Verbindungen| Importiert Verbindungen aus einer Datei, die Verbindungen angibt.|
|Verbindungen exportieren| Exportiert Verbindungen in eine Datei.|
|Hinzufügen der Verbindung hinzufügen| Fügt eine neue Kusto-Server Verbindung hinzu.| 
|Bearbeiten der Verbindung| Öffnet ein Dialogfeld für die Verbindungs Eigenschaften der Kusto-Server Verbindung.|
|Verbindung entfernen| Entfernt die vorhandene Verbindung mit dem Kusto-Server.|
|Aktualisieren| Aktualisiert die Eigenschaften einer Kusto-Server Verbindung.|
||---------*Identitäts Anbieter*---------|
|Verbindungs Prinzipal überprüfen| Zeigt die aktiven Benutzer Details der Ströme an|
|Abmelden von Aad| Meldet den aktuellen Benutzer von der Verbindung mit Aad ab.|
||---------*Datenbereich*---------|
|Cache Bereich|<ul><li>Hot dataexecute-Abfragen nur für den [Hot-Data-Cache](../management/cachepolicy.md)</li><li>Alle Daten: Ausführen von Abfragen für alle verfügbaren Daten (Standard)</li></ul> |
|Datetime-Spalte| Der Name der Spalte, die für einen Zeit präfilter verwendet werden kann.|
|Zeit Filter| Wert der Zeit vor dem Filtern|

### <a name="view-tab"></a>Registerkarte anzeigen

![Registerkarte anzeigen](./Images/KustoTools-KustoExplorer/view-tab.png "Registerkarte "Ansicht"")

|Menü|Verhalten|
|----|----------|
||---------*Auftritts*---------|
|Vollständiger Ansichtsmodus | Maximiert den Arbeitsbereich, indem das Menü Band Menü und der Verbindungs Panel ausgeblendet werden.|
|Schriftart vergrößern  | Vergrößert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|  
|Schriftart verkleinern  | Verringert die Schriftgröße der Registerkarte "Abfrage" und des Ergebnisdaten Rasters.|
|Layout zurücksetzen|Setzt das Layout der Andock Steuerelemente und Fenster des Tools zurück.|
||---------*Datenansicht*---------|
|Ansicht zurücksetzen| Setzt Daten Ansichts Einstellungen zurück (*).|
|Untersuchen von Spaltenwerten|Zeigt die Verteilung von Spaltenwerten an|
|Fokus auf Abfrage Statistik|Ändert den Fokus auf Abfrage Statistik anstelle von Abfrage Ergebnissen nach Abschluss der Abfrage.|
|Duplikate ausblenden|Schaltet die Entfernung der doppelten Zeilen aus den Abfrage Ergebnissen um.|
|Leere Spalten ausblenden|Schaltet das Entfernen leerer Spalten aus den Abfrage Ergebnissen um.|
|Einzelne Spalten zuklappen|Schaltet reduzierende Spalten mit einem Singular Wert um.|
||---------*Datenfilterung*---------|
|Filtern von Zeilen in der Suche|Schaltet die Option zum Anzeigen von nur übereinstimmenden Zeilen in der Abfrageergebnis Suche um (STRG + F).|
||---------*Visualisierungen*---------|
|Visualisierungen|Siehe [Visualisierungen](#visualizations-section)weiter oben. |

(*) Daten Ansichts Einstellungen: Kusto. Explorer verfolgt, welche Einstellungen pro eindeutigem Satz von Spalten verwendet werden. Wenn Spalten neu angeordnet oder entfernt werden, wird die Daten Sicht gespeichert und immer dann wieder verwendet, wenn die Daten mit denselben Spalten abgerufen werden. Um die Ansicht auf die Standardwerte zurückzusetzen, wählen Sie auf der Registerkarte **Ansicht** die Option **Ansicht zurücksetzen**aus. 

### <a name="tools-tab"></a>Registerkarte „Tools“

![Registerkarte Extras](./Images/KustoTools-KustoExplorer/tools-tab.png "Registerkarte "Tools"")

|Menü|Verhalten|
|----|----------|
||---------*IntelliSense*---------|
|IntelliSense aktivieren| Aktiviert und deaktiviert IntelliSense im Skript Panel.|
||---------*Analy*---------|
|Query Analyzer| Hiermit wird das Abfrage Analysetool gestartet.|
|Abfrage Prüfung | Analysiert die aktuelle Abfrage und gibt eine Reihe von anwendbaren Verbesserungsempfehlungen aus.|
|Calculator| Starten des Rechners|
||---------*Analytik*---------|
|Analytische Berichte| Öffnet ein Dashboard mit mehreren vorgefertigten Berichten für die Datenanalyse.|
||---------*Übersetzen*---------|
|Abfrage zum Power BI| Übersetzt eine Abfrage in ein Format, das für die Verwendung von in geeignet ist Power BI|
||---------*Optionen*---------|
|Optionen zurücksetzen| Legt die Anwendungseinstellungen auf die Standardwerte fest.|
|Optionen| Öffnet ein Tool zum Konfigurieren von Anwendungseinstellungen. [Details](kusto-explorer-options.md)|



### <a name="management-tab"></a>Verwaltungs Registerkarte

![Verwaltungs Registerkarte](./Images/KustoTools-KustoExplorer/management-tab.png "Registerkarte "Verwaltung"")

|Menü             | Verhalten|
|-----------------|---------|
||---------*Autorisierte Prinzipale*---------|
|Verwalten von autorisierten clusterprinzipale |Ermöglicht das Verwalten der Prinzipale eines Clusters für autorisierte Benutzer.| 
|Autorisierte autorisierte Daten Bank Prinzipale verwalten | Ermöglicht das Verwalten der Prinzipale einer Datenbank für autorisierte Benutzer.| 
|Verwalten von autorisierten Tabellen Prinzipale | Ermöglicht das Verwalten der Prinzipale einer Tabelle für autorisierte Benutzer.| 
|Verwalten von autorisierten Funktions Prinzipale | Ermöglicht das Verwalten der Prinzipale einer Funktion für autorisierte Benutzer.| 

### <a name="help-tab"></a>Hilfe Registerkarte

![Hilfe Registerkarte](./Images/KustoTools-KustoExplorer/help-tab.png "Hilfe-Registerkarte")

|Menü             | Verhalten|
|-----------------|---------|
||---------*Dokumentation*---------|
|Hilfe             | Öffnet einen Link zur Kusto-Online Dokumentation.  | 
|Neues       | Öffnet ein Dokument, in dem alle Änderungen an Kusto. Explorer aufgelistet sind.|
|Report Issue (Problem melden)      |Öffnet ein Dialogfeld mit zwei Optionen: <ul><li>Probleme im Zusammenhang mit dem Dienst melden</li><li>Melden von Problemen in der Client Anwendung</li></ul> | 
|Feature vorschlagen  | Öffnet einen Link zum Kusto-Feedback Forum. | 
|Updates überprüfen     | Prüft, ob Updates für Ihre Version von Kusto. Explorer vorhanden sind. | 

## <a name="connections-panel"></a>Verbindungs Panel

![alt text](./Images/KustoTools-KustoExplorer/connectionsPanel.png "Verbindungs Panel") 

Im linken Bereich von Kusto. Explorer werden alle Cluster Verbindungen angezeigt, mit denen der Client konfiguriert ist. Für jeden Cluster werden die Datenbanken, Tabellen und Attribute (Spalten) angezeigt, die Sie speichern. Der Bereich Verbindungen ermöglicht es Ihnen, Elemente auszuwählen (wodurch ein impliziter Kontext für die Suche/Abfrage im Hauptbereich festgelegt wird), oder Doppelklicken Sie auf Elemente, um den Namen in das Such-/abfragenpanel zu kopieren.

Wenn das tatsächliche Schema sehr groß ist (z. b. eine Datenbank mit Hunderten von Tabellen), ist es möglich, das Schema zu durchsuchen, indem Sie STRG + F drücken und eine Teil Zeichenfolge (ohne Beachtung der Groß-/Kleinschreibung) des gesuchten Entitäts Namens eingeben.

"Kusto. Explorer" unterstützt das Steuern des Verbindungs Bereichs über das Abfragefenster.
Dies ist für Skripts sehr nützlich. Wenn Sie z. b. eine Skriptdatei mit einem Befehl starten, der Kusto. Explorer anweist, eine Verbindung mit dem Cluster bzw. der Datenbank herzustellen, deren Daten durch das Skript abgefragt werden, können Sie die folgende Syntax verwenden. Wie üblich müssen Sie jede Zeile mit `F5` oder ähnlich ausführen:

```kusto
#connect cluster('help').database('Samples')

StormEvents | count
```

### <a name="controlling-the-user-identity-used-for-connecting-to-kusto"></a>Steuern der Benutzeridentität, die zum Herstellen einer Verbindung mit Kusto verwendet wird

Beim Hinzufügen einer neuen Verbindung wird das Standard Sicherheitsmodell mit Aad-Verbund Sicherheit verwendet, bei dem die Authentifizierung über die Azure Active Directory mithilfe der standardmäßigen Aad-Benutzerfunktion durchgeführt wird.

In einigen Fällen benötigen Sie möglicherweise eine präzisere Steuerung der Authentifizierungs Parameter, als in Aad verfügbar ist. Wenn dies der Fall ist, können Sie das Bearbeitungsfeld "Erweiterte Verbindungs Zeichenfolgen" erweitern und einen gültigen [Kusto-Verbindungs Zeichenfolgen](../api/connection-strings/kusto.md) -Wert angeben.

Beispielsweise müssen Benutzer, die in mehreren Aad-Mandanten vorhanden sind, mitunter eine bestimmte "Projektion" ihrer Identitäten für einen bestimmten Aad-Mandanten verwenden. Hierzu können Sie eine Verbindungs Zeichenfolge wie die folgende angeben (Wörter in Großbuchstaben durch bestimmte Werte ersetzen):

```
Data Source=https://CLUSTER_NAME.kusto.windows.net;Initial Catalog=DATABASE_NAME;AAD Federated Security=True;Authority Id=AAD_TENANT_OF_CLUSTER;User=USER_DOMAIN
```

Das ist eindeutig, `AAD_TENANT_OF_CLUSTER` Wenn ein Domänen Name oder eine Aad-Mandanten-ID (eine GUID) des Aad-Mandanten ist, in dem der Cluster gehostet wird (in der Regel der Name der Organisations Domäne, der den Cluster besitzt, z. b. `contoso.com` ), und USER_DOMAIN die Identität des Benutzers ist, der in diesen Mandanten eingeladen wurde (z `joe@fabrikam.com` . b.). 

>[!Note]
> Der Domänen Name des Benutzers ist nicht notwendigerweise identisch mit dem des Mandanten, der den Cluster gehostet.

## <a name="table-row-colors"></a>Tabellenzeilen Farben

"Kusto. Explorer" versucht, den Schweregrad oder den ausführlichkeits Grad der einzelnen Zeilen im Ergebnisbereich zu "erraten" und entsprechend zu färben. Dies geschieht, indem die unterschiedlichen Werte der einzelnen Spalten mit einem Satz bekannter Muster ("Warnung", "Fehler" usw.) abgeglichen werden.

Um das Ausgabe Farbschema zu ändern oder dieses Verhalten zu deaktivieren, wählen Sie **im Menü Extras** die **Option Optionen**  >  **Ergebnis-Viewer**ausführlichkeits  >  **Farbschema**aus.

![alt text](./Images/KustoTools-KustoExplorer/ke-color-scheme.png)

## <a name="search-mode"></a>Such Modus + +

1. Wählen Sie auf der Registerkarte Startseite in der Dropdown Liste Abfrage den Wert "suchen + +" aus.
2. Wählen Sie **mehrere Tabellen** aus, und definieren Sie dann unter **Tabellen auswählen**die zu durchsuchenden Tabellen.
3. Geben Sie im Bearbeitungsfeld Ihren Suchbegriff ein, und wählen Sie **Los** aus.
4. Eine wärmemap des Tabellen-/Uhrzeit-slotrasters zeigt an, welcher Begriff angezeigt wird und wo Sie angezeigt werden.
5. Wählen Sie im Raster eine Zelle aus, und wählen Sie **Details anzeigen** aus, um die relevanten Einträge anzuzeigen.

![alt text](./Images/KustoTools-KustoExplorer/ke-search-beta.jpg "KE-Search-Beta") 

## <a name="query-mode"></a>Abfragemodus

Kusto. Explorer verfügt über einen leistungsstarken Skript Modus, mit dem Sie Ad-hoc-Abfragen schreiben, bearbeiten und ausführen können. Der Skript Modus verfügt über Syntax Hervorhebung und IntelliSense, sodass Sie schnell zu Kusto CSL-Sprache wechseln können.

### <a name="basic-queries"></a>Standardabfragen

Wenn Sie über Tabellen Protokolle verfügen, können Sie mit der Untersuchung beginnen, indem Sie Folgendes eingeben:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | count 
```

Wenn sich der Cursor in dieser Zeile befindet, ist er grau gefärbt. Durch Drücken von "F5" wird die Abfrage ausgeführt. 

Im folgenden finden Sie einige Beispiele für Abfragen:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

## <a name="importing-a-local-file-into-a-kusto-table"></a>Importieren einer lokalen Datei in eine Kusto-Tabelle

Kusto. Explorer bietet eine bequeme Möglichkeit zum Hochladen von Dateien von Ihrem Computer in eine Kusto-Tabelle.

1. Stellen Sie sicher, dass Sie die Tabelle mit einem Schema erstellt haben, das Ihrer Datei entspricht (z. b. mit dem Befehl " [. Create Table](../management/tables.md) ").

1. Stellen Sie sicher, dass die Dateierweiterung dem Inhalt der Datei entspricht. Zum Beispiel:
    * Wenn die Datei durch Trennzeichen getrennte Werte enthält, stellen Sie sicher, dass die Datei eine CSV-Dateierweiterung aufweist.
    * Wenn die Datei durch Tabstopps getrennte Werte enthält, stellen Sie sicher, dass die Datei die Erweiterung. TSV hat.

1. Klicken Sie im Bereich [Verbindungen](#connections-panel)mit der rechten Maustaste auf die Zieldatenbank, und wählen Sie **Aktualisieren**aus, damit die Tabelle angezeigt wird.

    ![alt text](./Images/KustoTools-KustoExplorer/right-click-refresh-schema.png "Klicken Sie mit der rechten Maustaste auf-Refresh-Schema.")

1. Klicken Sie im Bereich [Verbindungen](#connections-panel)mit der rechten Maustaste auf die Ziel Tabelle, und wählen Sie **Daten aus lokalen Dateien importieren aus**.

    ![alt text](./Images/KustoTools-KustoExplorer/right-click-import-local-file.png "Klicken Sie mit der rechten Maustaste auf Import-local-file.")

1. Wählen Sie die hoch zuladenden Dateien aus, und klicken Sie auf **Öffnen**.

    ![alt text](./Images/KustoTools-KustoExplorer/import-local-file-choose-files.png "Import-local-file-Choose-files")

    Die Statusanzeige zeigt den Fortschritt an, und ein Dialogfeld wird angezeigt, wenn der Vorgang abgeschlossen ist.

    ![alt text](./Images/KustoTools-KustoExplorer/import-local-file-progress.png "Import-local-file-Progress")

    ![alt text](./Images/KustoTools-KustoExplorer/import-local-file-complete.png "Import-local-file-Complete")

1. Fragen Sie die Daten in der Tabelle ab (Doppelklicken Sie im Bereich [Verbindungen](#connections-panel)auf die Tabelle).

## <a name="managing-authorized-principals"></a>Verwalten von autorisierten Prinzipale

"Kusto. Explorer" stellt eine bequeme Möglichkeit zum Verwalten von autorisierten Prinzipale in Clustern, Datenbanken, Tabellen oder Funktionen dar.

> [!Note]
> Nur [Administratoren](../management/access-control/role-based-authorization.md) können autorisierte Prinzipale in Ihrem eigenen Gültigkeitsbereich hinzufügen oder löschen.

1. Klicken Sie im Bereich [Verbindungen](#connections-panel)mit der rechten Maustaste auf die Ziel Entität, und wählen Sie **autorisierte Prinzipale verwalten**aus. (Sie können dies auch über das Menü "Verwaltung" tun.)

    ![alt text](./Images/KustoTools-KustoExplorer/right-click-manage-authorized-principals.png "Klicken Sie mit der rechten Maustaste auf-Manage-Authorized-Principals")

    ![alt text](./Images/KustoTools-KustoExplorer/manage-authorized-principals-window.png "Verwalten-autorisierte-Principals-Fenster")

1. Zum Hinzufügen eines neuen autorisierten Prinzipals wählen Sie **Prinzipal hinzufügen**aus, geben die Prinzipal Details an und bestätigen die Aktion.

    ![alt text](./Images/KustoTools-KustoExplorer/add-authorized-principals-window.png "Add-Authorized-Principals-Fenster")

    ![alt text](./Images/KustoTools-KustoExplorer/confirm-add-authorized-principals.png "Confirm-Add-Authorized-Principals")

1. Um einen vorhandenen autorisierten Prinzipal zu löschen, wählen Sie **Drop Principal** aus, und bestätigen Sie die Aktion.

    ![alt text](./Images/KustoTools-KustoExplorer/confirm-drop-authorized-principals.png "bestätigen: Drop-Authorized-Principals")

## <a name="sharing-queries-and-results-by-email"></a>Freigeben von Abfragen und Ergebnissen per e-Mail

"Kusto. Explorer" stellt eine bequeme Möglichkeit dar, Abfragen und Abfrageergebnisse per e-Mail freizugeben. Wählen Sie **in Zwischenablage exportieren**aus, und die folgenden Elemente werden von Kusto. Explorer in die Zwischenablage kopiert:
1. Ihre Abfrage
1. Die Abfrageergebnisse (Tabelle oder Diagramm)
1. Verbindungsdetails für den Kusto-Cluster und die Datenbank
1. Ein Link, mit dem die Abfrage automatisch erneut ausgeführt wird.

Funktionsweise:

1. Ausführen einer Abfrage in "Kusto. Explorer"
1. Wählen Sie **in Zwischenablage exportieren** aus (oder drücken Sie `Ctrl+Shift+C` )

    ![alt text](./Images/KustoTools-KustoExplorer/menu-export.png "Menü-Export")

1. Öffnen Sie z. b. eine neue Outlook-Nachricht.

    ![alt text](./Images/KustoTools-KustoExplorer/share-results.png "Freigabe-Ergebnisse")
    
1. Fügen Sie den Inhalt der Zwischenablage in die Outlook-Nachricht ein.

    ![alt text](./Images/KustoTools-KustoExplorer/share-results-2.png "Freigabe-results-2")

## <a name="client-side-query-parametrization"></a>Client seitige Abfrage Parametrisierung

> [!WARNING]
> Es gibt zwei Typen von Abfrage parametrisierungstechniken in Kusto:
> * Die [sprach integrierte Abfrage Parametrisierung](../query/queryparametersstatement.md) wird als Teil der Abfrage-Engine implementiert und soll von Anwendungen verwendet werden, die den Dienstprogramm gesteuert Abfragen.
>
> * Die nachstehend beschriebene Client seitige Abfrage Parametrisierung ist eine Funktion der Anwendung "Kusto. Explorer". Dies entspricht der Verwendung von String-Replace-Vorgängen für die Abfragen, bevor Sie von dem Dienst ausgeführt werden. Die unten beschriebene Syntax ist nicht Bestandteil der Abfragesprache und kann nicht verwendet werden, wenn Abfragen an den Dienst nicht über "Kusto. Explorer" gesendet werden.

Wenn Sie beabsichtigen, denselben Wert in mehreren Abfragen oder auf mehreren Registerkarten zu verwenden, ist es schwierig, Sie zu ändern. Allerdings unterstützt Kusto. Explorer Abfrage Parameter. Parameter werden durch eckige {} Klammern bezeichnet. Beispiel: `{parameter1}`

Der Skript-Editor hebt die Abfrage Parameter hervor:

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-1.png "parametzed-Query-1")

Sie können vorhandene Abfrage Parameter problemlos definieren und bearbeiten:

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-2.png "parametzed-Query-2")

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-3.png "parametzed-Query-3")

Der Skript-Editor verfügt auch über IntelliSense für bereits definierte Abfrage Parameter:

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-4.png "parametzed-Query-4")

Es können mehrere Parametersätze vorhanden sein (im Kombinations Feld **Parametersatz** aufgeführt).
Verwenden **Sie die Add New** und **Delete Current** , um die Liste der Parameter Sätze zu bearbeiten.

![alt text](./Images/KustoTools-KustoExplorer/parametrized-query-5.png "parametzed-Query-5")

Abfrage Parameter werden von Registerkarten gemeinsam genutzt, sodass Sie problemlos wieder verwendet werden können.

## <a name="deep-linking-queries"></a>Deep-Linking-Abfragen

### <a name="overview"></a>Übersicht
Sie können einen URI erstellen, der, wenn er in einem Browser geöffnet wird, "Kusto. Explorer" lokal startet und eine bestimmte Abfrage für eine angegebene Kusto-Datenbank durchführt.

### <a name="limitations"></a>Einschränkungen
Die Abfragen sind aufgrund von Internet Explorer-Einschränkungen auf ~ 2000 Zeichen beschränkt (die Einschränkung ist ungefähre Werte, da Sie von der Länge des Clusters und des Daten Banknamens abhängig ist), https://support.microsoft.com/kb/208427 um die Wahrscheinlichkeit zu verringern, dass Sie die Zeichen Beschränkung erreichen, finden Sie weiter unten unter [Getting Kürzungs Links](#getting-shorter-links).

Das Format des URIs lautet: https:// <ClusterCname> . Kusto.Windows.net/ <DatabaseName> ? Query =<QueryToExecute>

Beispiel:  https://help.kusto.windows.net/Samples?query=StormEvents+%7c+limit+10
 
Dieser URI öffnet "Kusto. Explorer", stellt eine Verbindung mit dem `help` Kusto-Cluster her und führt die angegebene Abfrage für die `Samples` Datenbank aus. Wenn bereits eine Instanz von Kusto. Explorer ausgeführt wird, wird die laufende Instanz eine neue Registerkarte öffnen und die Abfrage darin ausführen.)

**Sicherheitshinweis**: aus Sicherheitsgründen ist das Deep-Linking für Steuerungsbefehle deaktiviert.



### <a name="creating-a-deep-link"></a>Erstellen eines Deep-Links
Die einfachste Möglichkeit, einen Deep-Link zu erstellen, besteht darin, die Abfrage in Kusto. Explorer zu erstellen und dann mithilfe von Export in die Zwischenablage zu kopieren, um die Abfrage (einschließlich Deep-Link und Ergebnisse) in die Zwischenablage zu kopieren. Sie können Sie dann per e-Mail freigeben.
        
Beim Kopieren in eine e-Mail wird der Deep-Link in kleinen Schriftarten angezeigt. Zum Beispiel:

https://help.kusto.windows.net:443/Samples[[Zum Ausführen der Abfrage klicken](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

Der erste Link öffnet Kusto. Explorer und legt den Cluster und den Daten Bank Kontext entsprechend fest.
Der zweite Link (Klicken Sie, um die Abfrage auszuführen) ist der Deep-Link. Wenn Sie zum Link zu einer e-Mail-Nachricht wechseln und STRG + K drücken, wird die tatsächliche URL angezeigt:

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

### <a name="deep-links-and-parametrized-queries"></a>Deep-Links und Paramete Abfragen

Paramete Abfragen können mit Deep-Linking verwendet werden.

1. Erstellen Sie eine Abfrage, die als Parameterabfrage geformt werden soll (z. b. `KustoLogs | where Timestamp > ago({Period}) | count` ). 
2. Geben Sie in diesem Fall einen Parameter für jeden Abfrage Parameter im URI an:

`https://mycluster.kusto.windows.net/MyDatabase?web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

### <a name="getting-shorter-links"></a>Erhalten kürzerer Links

Abfragen können zu lang sein. Um die Wahrscheinlichkeit zu verringern, dass die Abfrage die maximale Länge überschreitet, verwenden Sie die `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` in der Kusto-Client Bibliothek verfügbare Methode Diese Methode erzeugt eine kompaktere Version der Abfrage. Das kürzere Format wird auch von Kusto. Explorer erkannt.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

Die Abfrage wird durch Anwenden der nächsten Transformation kompakter:

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Kusto. Explorer-Befehlszeilenargumente

"Kusto. Explorer" unterstützt mehrere Befehlszeilenargumente in der folgenden Syntax (die Reihenfolge spielt eine Rolle):

[*Localscriptfile*] [*QueryString*]

Hierbei gilt:
* *Localscriptfile* ist der Name einer Skriptdatei auf dem lokalen Computer, die die Erweiterung aufweisen muss `.kql` . Wenn eine solche Datei vorhanden ist, lädt Kusto. Explorer diese Datei automatisch, wenn Sie gestartet wird.
* *QueryString* ist eine Zeichenfolge, die mit HTTP-Abfrage Zeichenfolgen formatiert wird Diese Methode stellt zusätzliche Eigenschaften bereit, wie in der folgenden Tabelle beschrieben.

Wenn Sie z. b. "Kusto. Explorer" mit einer Skriptdatei starten möchten, `c:\temp\script.kql` die für die Kommunikation mit dem Cluster und `help` der Datenbank konfiguriert `Samples` ist, verwenden Sie den folgenden Befehl:

```
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|Argument  |Beschreibung                                                               |
|----------|--------------------------------------------------------------------------|
|**Auszuführende Abfrage**                                                                 |
|`query`   |Die auszuführende Abfrage (base64-codiert). Wenn leer, verwenden Sie `querysrc` .          |
|`querysrc`|Die URL einer Datei oder eines BLOBs, die die auszuführende Abfrage enthält (wenn `query` leer ist).|
|**Verbindung mit dem Kusto-Cluster**                                                  |
|`uri`     |Die Verbindungs Zeichenfolge des Kusto-Clusters, mit dem eine Verbindung hergestellt wird.                 |
|`name`    |Der Anzeige Name der Verbindung mit dem Kusto-Cluster.                  |
|**Verbindungsgruppe**                                                                 |
|`path`    |Die URL der herunter zuladenden Verbindungs Gruppendatei (URL-codiert).             |
|`group`   |Der Name der Verbindungsgruppe.                                         |
|`filename`|Die lokale Datei, die die Verbindungsgruppe enthält.                              |

## <a name="kustoexplorer-connection-files"></a>Kusto. Explorer-Verbindungs Dateien

Kusto. Explorer behält seine Verbindungseinstellungen im `%LOCALAPPDATA%\Kusto.Explorer` Ordner.
Eine Liste der Verbindungs Gruppen wird in beibehalten `%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml` , und jede Verbindungsgruppe wird in einer dedizierten Datei unter gespeichert `%LOCALAPPDATA%\Kusto.Explorer\Connections\` .

### <a name="format-of-connection-group-files"></a>Format der Verbindungs Gruppen Dateien

Der Speicherort der Datei ist `%LOCALAPPDATA%\Kusto.Explorer\UserConnectionGroups.xml` .  

Dies ist eine XML-Serialisierung eines Arrays von `ServerGroupDescription` Objekten mit den folgenden Eigenschaften:

```
  <ServerGroupDescription>
    <Name>`Connection Group name`</Name>
    <Details>`Full path to XML file containing the list of connections`</Details>
  </ServerGroupDescription>
```

Beispiel:

```
<?xml version="1.0"?>
<ArrayOfServerGroupDescription xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerGroupDescription>
    <Name>Connections</Name>
    <Details>C:\Users\alexans\AppData\Local\Kusto.Explorer\UserConnections.xml</Details>
  </ServerGroupDescription>
</ArrayOfServerGroupDescription>  
```

### <a name="format-of-connection-list-files"></a>Format der Verbindungs Listen Dateien

Datei Speicherort: `%LOCALAPPDATA%\Kusto.Explorer\Connections\` .

Dies ist eine XML-Serialisierung eines Arrays von `ServerDescriptionBase` Objekten mit den folgenden Eigenschaften:

```
   <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>`Connection name`</Name>
    <Details>`Details as shown in UX, usually full URI`</Details>
    <ConnectionString>`Full connection string to the cluster`</ConnectionString>
  </ServerDescriptionBase>
```

Beispiel:

```
<?xml version="1.0"?>
<ArrayOfServerDescriptionBase xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <ServerDescriptionBase xsi:type="ServerDescription">
    <Name>Help</Name>
    <Details>https://help.kusto.windows.net</Details>
    <ConnectionString>Data Source=https://help.kusto.windows.net:443;Federated Security=True</ConnectionString>
  </ServerDescriptionBase>
</ArrayOfServerDescriptionBase>
```

## <a name="resetting-kustoexplorer"></a>Zurücksetzen von "Kusto. Explorer"

Wenn erforderlich, können Sie Kusto. Explorer vollständig zurücksetzen. Gehen Sie folgendermaßen vor, um den auf Ihrem Computer bereitgestellten Kusto. Explorer progressiv zurückzusetzen, bis er vollständig entfernt wurde und von Grund auf neu installiert werden muss.

1. Öffnen **oder entfernen** Sie unter Windows die Option Programme (auch als **Programme und Funktionen**bezeichnet).
1. Wählen Sie jedes Element aus, dessen Name mit beginnt `Kusto.Explorer` .
1. Wählen Sie **Deinstallieren** aus.

   Wenn die Anwendung nicht deinstalliert werden kann (ein bekanntes Problem tritt manchmal bei ClickOnce-Anwendungen auf), lesen Sie [diesen Stapelüberlauf Artikel](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer) , in dem erläutert wird, wie Sie dies tun.

1. Löschen Sie den Ordner `%LOCALAPPDATA%\Kusto.Explorer` . Dadurch werden alle Verbindungen, der Verlauf usw. entfernt.

1. Löschen Sie den Ordner `%APPDATA%\Kusto` . Dadurch wird der Kusto. Explorer-Tokencache entfernt. Sie müssen sich erneut bei allen Clustern authentifizieren.

Es ist auch möglich, zu einer bestimmten Version von Kusto. Explorer zurückzukehren:

1. Führen Sie aus `appwiz.cpl`.
1. Wählen Sie **Kusto. Explorer** und anschließend **Deinstallieren/ändern**aus.
3. Wählen Sie **Wiederherstellen des vorherigen Zustands der Anwendung**aus.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="kustoexplorer-fails-to-start"></a>"Kusto. Explorer" kann nicht gestartet werden.

#### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>"Kusto. Explorer" zeigt ein Fehler Dialogfeld während oder nach dem Start an.

**Anzeichen:**

Beim Start zeigt Kusto. Explorer einen `InvalidOperationException` Fehler an.

**Mögliche Lösung:**

Dieser Fehler kann darauf hindeuten, dass das Betriebssystem beschädigt wurde oder einige der grundlegenden Module fehlt.
Um fehlende oder beschädigte Systemdateien zu überprüfen, führen Sie die folgenden Schritte aus:   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

### <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>"Kusto. Explorer" wird immer heruntergeladen, auch wenn keine Updates vorhanden sind.

**Anzeichen:**

Jedes Mal, wenn Sie Kusto. Explorer öffnen, werden Sie aufgefordert, eine neue Verison zu installieren. "Kusto. Explorer" lädt das gesamte Paket herunter, ohne dass die bereits installierte Version tatsächlich aktualisiert wird.

**Mögliche Lösung:**

Dies könnte auf eine Beschädigung in Ihrem lokalen ClickOnce-Speicher zurückzuführen sein. Sie können den lokalen ClickOnce-Speicher löschen, indem Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten ausführen.
> [!Important]
> 1. Wenn andere Instanzen von ClickOnce-Anwendungen oder von vorhanden sind `dfsvc.exe` , beenden Sie diese, bevor Sie diesen Befehl ausführen.
> 2. Alle ClickOnce-apps werden automatisch neu installiert, wenn Sie Sie das nächste Mal ausführen, sofern Sie auf den ursprünglichen in der APP-Verknüpfung gespeicherten Installationsort zugreifen können. App-Verknüpfungen werden nicht gelöscht.

```
rd /q /s %userprofile%\appdata\local\apps\2.0
```

Versuchen Sie, Kusto. Explorer erneut von einem der [Installations Spiegelungen](#getting-the-tool)zu installieren.

#### <a name="clickonce-error-cannot-start-application"></a>ClickOnce-Fehler: die Anwendung kann nicht gestartet werden.

**On**  

* Das Programm kann nicht gestartet werden und zeigt einen Fehler an, der Folgendes enthält:`External component has thrown an exception`
* Das Programm kann nicht gestartet werden und zeigt einen Fehler an, der Folgendes enthält:`Value does not fall within the expected range`
* Das Programm kann nicht gestartet werden und zeigt einen Fehler an, der Folgendes enthält:`The application binding data format is invalid.` 
* Das Programm kann nicht gestartet werden und zeigt einen Fehler an, der Folgendes enthält:`Exception from HRESULT: 0x800736B2`

Sie können die Fehlerdetails durch Klicken `Details` auf das folgende Fehler Dialogfeld Durchsuchen:

![alt text](./Images/KustoTools-KustoExplorer/clickonce-err-1.jpg "ClickOnce-err-1")

```
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**Vorgeschlagene Lösungsschritte:**

1. Deinstallieren Sie die Anwendung "Kusto. Explorer" mithilfe von `Programs and Features` ( `appwiz.cpl` ).

1. Führen Sie aus `CleanOnlineAppCache` , und versuchen Sie dann erneut, Kusto. Explorer zu installieren. An einer Eingabeaufforderung mit erhöhten Rechten: 
    
    ```
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    Installieren Sie Kusto. Explorer erneut von einem der [Installations Spiegelungen](#getting-the-tool).

1. Wenn der Fehler weiterhin auftritt, löschen Sie den lokalen ClickOnce-Speicher. Alle ClickOnce-apps werden automatisch neu installiert, wenn Sie Sie das nächste Mal ausführen, sofern Sie auf den ursprünglichen in der APP-Verknüpfung gespeicherten Installationsort zugreifen können. App-Verknüpfungen werden nicht gelöscht.

An einer Eingabeaufforderung mit erhöhten Rechten:

    ```
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Install Kusto.Explorer again from one of the [installation mirrors](#getting-the-tool)

1. Wenn der Fehler weiterhin auftritt, entfernen Sie temporäre Bereitstellungs Dateien, und benennen Sie den lokalen AppData-Ordner "Kusto. Explorer" um

    An einer Eingabeaufforderung mit erhöhten Rechten:

    ```
    rd /s/q %userprofile%\AppData\Local\Temp\Deployment
    ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
    ```

    Installieren Sie Kusto. Explorer erneut von einem der [Installations Spiegelungen](#getting-the-tool) .

1. Zum Wiederherstellen der Verbindungen aus "Kusto. Explorer. bak" über eine Eingabeaufforderung mit erhöhten Rechten:

    ```
    copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
    ```

1. Wenn der Fehler weiterhin auftritt, aktivieren Sie die ausführliche ClickOnce-Protokollierung, indem Sie den Zeichen folgen Wert "LogVerbosityLevel" 1 unter:

`HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment`, wiederholen Sie den Vorgang erneut, und senden Sie die ausführliche Ausgabe an KEBugReport@microsoft.com . 

#### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce-Fehler: Ihr Administrator hat diese Anwendung blockiert, da Sie potenziell ein Sicherheitsrisiko für Ihren Computer darstellt.

**Anzeichen:**  
Das Programm kann nicht mit einem der folgenden Fehler installiert werden:
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**Lösung:**

1. Dies kann daran liegen, dass eine andere Anwendung das Standardverhalten der ClickOnce-Vertrauens Aufforderung überschreibt. Sie können Ihre Standard Konfigurationseinstellungen anzeigen, Sie mit den tatsächlichen Einstellungen auf Ihrem Computer vergleichen und Sie nach Bedarf zurücksetzen, wie [in diesem Artikel](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)erläutert.

#### <a name="cleanup-application-data"></a>Bereinigen von Anwendungsdaten

In einigen Fällen, in denen die vorherigen Schritte zur Problembehandlung beim Einstieg von Kusto. Explorer nicht helfen konnten, kann das Bereinigen von lokal gespeicherten Daten helfen.

Die von der Anwendung "Kusto. Explorer" gespeicherten Daten finden Sie hier: `C:\Users\\[your alias]\AppData\Local\Kusto.Explorer` .

> [!NOTE]
> Das Bereinigen der Daten führt zu einem Verlust der geöffneten Registerkarten (Wiederherstellungs Ordner), gespeicherter Verbindungen (Verbindungs Ordner) und Anwendungseinstellungen (Ordner "UserSettings").