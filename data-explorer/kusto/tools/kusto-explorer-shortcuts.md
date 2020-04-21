---
title: Kusto.Explorer-Tastenkombinationen (Hotkeys) - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Kusto.Explorer-Tastaturbefehle (Hotkeys) in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/22/2020
ms.openlocfilehash: 81d99d14831905681c2dbbc45aea4b63134a06a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523918"
---
# <a name="kustoexplorer-keyboard-shortcuts-hot-keys"></a>Kusto.Explorer Tastenkombinationen (Hotkeys)

## <a name="application-level"></a>Anwendungsebene

Die folgenden Tastenkombinationen können aus jedem Kontext verwendet werden:

|Hot Key|Beschreibung|
|-------|-----------|
|`F1`     | Öffnet die Hilfe|
|`F11`    | Umschalten des Vollansichtsmodus|
|`Ctrl`+`F1`| Umschalten des Farbbandauftritts |
|`Ctrl`+`+`| Erhöht die Schriftart "Abfrage- und Datenergebnisse"|
|`Ctrl`+`-`| Verringert Abfrage- und Datenergebnisschriftart|
|`Ctrl`+`0`| Zurücksetzt Abfrage- und Datenergebnisschriftart|
|`Ctrl`+`1` .. `7`| Wechselt zum Abfragebedienfeld mit der entsprechenden Nummer (1..7)|
|`Ctrl`+`F2`|Umbenennung des Headers des Abfrage-Editor-Bedienfelds|
|`Ctrl`+`N` |Öffnet einen neuen Abfrageeditor|
|`Ctrl`+`O` |Öffnen des Abfrage-Editor-Skripts in einem neuen Abfrageeditor|
|`Ctrl`+`W` |Schließt den aktiven Abfrageeditor|
|`Ctrl`+`S` |Speichert Abfrage in einer Datei|
|`Shift`+`F3` | Öffnet Analytical Report Gallery|
|`Shift`+`F12`| Schaltet Licht/Dunkel-Thema der Anwendung um|
|`Ctrl`+`Shift`+`O`|Öffnet Kusto.Explorer-Optionen und -Einstellungen Dialog|
|`Esc`|Ausführen der Abfrage abbrechen|
|`Shift`+`F5`|Ausführen der Abfrage abbrechen|

## <a name="query-and-results-view"></a>Abfrage- und Ergebnisansicht

Die folgenden Tastenkombinationen können beim Bearbeiten einer Abfrage im Abfrageeditor oder beim Kontext in der Ergebnisansicht verwendet werden:

|Hot Key|Beschreibung|
|-------|-----------|
|`Ctrl`+`Shift`+`C`|Kopiert Abfrage, Deep-Link und Daten in die Zwischenablage|
|`Alt`+`Shift`+`C` |Kopiert die Abfrage und die Deep-Link-Zwischenablage im HTML-Format|
|`Alt`+`Shift`+`R` |Kopiert Abfrage, Deep-Link und Daten der Zwischenablage im Markdown-Format|
|`Alt`+`Shift`+`M` |Kopiert die Abfrage und die Deep-Link-Zwischenablage im Markdown-Format|
|`Ctrl`+`~` |Kopiert Abfragen und Daten in die Zwischenablage im Markdown-Format |
|`Ctrl`+`Shift`+`D`|Umschalten des Modus zum Ausblenden doppelter Zeilen in der Datenansicht|
|`Ctrl`+`Shift`+`H`|Umschalten des Modus zum Ausblenden leerer Spalten in der Datenansicht|
|`Ctrl`+`Shift`+`J`|Umschalten des Modus des Reduzierens von Spalten mit einem einzelnen Wert in der Datenansicht|
|`Ctrl`+`Shift`+`A`|Öffnet ein Query Analyzer-Tool in einem neuen Abfragebereich|
|`Alt`+`C`  |Rendert Säulendiagramm über vorhandene Daten|
|`Alt`+`T`  |Rendert Zeitleistendiagramm über vorhandene Daten|
|`Alt`+`A`  |Rendert Anomalie-Zeitachsendiagramm über vorhandene Daten|
|`Alt`+`P`  |Rendert Kreisdiagramm über vorhandene Daten|
|`Alt`+`L`  |Rendert Ladder-Timeline-Diagramm über vorhandene Daten|
|`Alt`+`V`  |Rendert Pivot-Diagramm über vorhandene Daten|
|`Ctrl`+`Shift`+`V`|Zeigt Timeline-Pivot über vorhandene Daten|
|`Ctrl`+`F9`  | / `highlight matching rows` `Ctrl`+`F`Schaltet Modi für Client-Textsuche ( ) Verhalten im Datenraster `show only matching rows` um. |
|`Ctrl`+`F10` |Zeigt das Detailbedienfeld an, in dem die ausgewählte Zeile als Eigenschaftsraster dargestellt wird|
|`Ctrl`+`F`  | Zeigt das Suchfeld für das Bedienfeld an, das sich derzeit im Fokus befindet. Unterstützt `Connetions`in `Data Results`, `Query Editor` und Panels|
|`Ctrl`+`Tab`| Zeigt das Dialogfeld Abfrage-Editor-Dokumentauswahl an. Sie können `Ctrl` Dokumente mit`Tab` |
|`Ctrl`+`J`|Umschalten des Erscheinungsbilds des Ergebnisfensters|
|`Ctrl`+`E`|Schaltet das Erscheinungsbild des Abfrage-Editors und des Ergebnisfensters im Zyklus von:`Query Editor and Results` -> `Query Editor` -> `Query Editor and Results` -> `Results` |
|`Ctrl`+`Shift`+`E`|Schaltet das Erscheinungsbild des Abfrage-Editors und des Ergebnisfensters im Zyklus von:`Query Editor and Results` -> `Results` -> `Query Editor and Results` -> `Query Editor` |
|`Ctrl`+`Shift`+`R` | Fokussiert auf das Ergebnispanel |
|`Ctrl`+`Shift`+`T` | Fokussiert auf das Connections-Bedienfeld |
|`Ctrl`+`Shift`+`Y` | Konzentriert sich auf den Abfrage-Editor |
|`Ctrl`+`Shift`+`P` | Fokussiert auf Diagramm-Panel |
|`Ctrl`+`Shift`+`I` | Konzentriert sich auf das Abfrageinformationsbedienfeld |
|`Ctrl`+`Shift`+`S` | Konzentriert sich auf das Bedienfeld Abfragestatistik |
|`Ctrl`+`Shift`+`K` | Konzentriert sich auf das Fehlerbedienfeld |
|`Alt`+`Ctrl`+`L`|Sperrt den aktuellen Verbindungskontext in den Abfrage-Editor, sodass das Ändern ausgewählter Zeilen im Kontext "Connetion" keine Auswirkungen auf den Kontext des Abfrage-Editors hat. |

## <a name="results-table-viewer"></a>Ergebnisse Tabellenanzeige

Die folgenden Tastenkombinationen können verwendet werden, wenn sich die Ergebnisansicht (Tabelle) im aktiven Tastaturfokus befindet:

|Hot Key|Beschreibung|
|-----------|-----------|
|`Ctrl`+`Q` |Aktuelles Spaltenkontextmenü anzeigen|
|`Ctrl`+`S` |Umschalten der aktuellen Spaltensortierung|
|`Ctrl`+`U` |Öffnet ein Bedienfeld mit aktuellen Spaltenwerten mit clientseitig erfilterung|
|`Ctrl`+`F` | Zeigt das Suchfeld für die Ergebnisse an|
|`Ctrl`+`F3`| / `highlight matching rows` `Ctrl`+`F`Schaltet Modi für Client-Textsuche ( ) Verhalten im Datenraster `show only matching rows` um. |

## <a name="query-editor"></a>Abfrage-Editor

Die folgenden Tastenkombinationen können beim Bearbeiten einer Abfrage im Abfrageeditor verwendet werden:

|Hot Key|Beschreibung|
|-------|-----------|
|`F1`|Wenn der Cursor auf einen Operator oder eine Funktion zeigt, öffnet sich ein Hilfefenster mit Informationen über den Operator oder die Funktion. Wenn das Hilfethema nicht vorhanden ist - öffnet eine Hilfe-URL|
|`F5`|Aktuell ausgewählte Abfrage ausführen|
|`Shift`+`Enter`|Aktuell ausgewählte Abfrage ausführen|
|`F8`|Abrufen von Abfrageergebnissen aus dem lokalen Cache. Wenn keine Ergebnisse vorhanden sind - Führen Sie die aktuell ausgewählte Abfrage aus.|
|`F6`|Aktuell ausgewählte Abfrage `Progressive Results` im Modus ausführen|
|`Ctrl`+`F5` | Vorschauergebnisse der ausgewählten Abfrage (zeigt nur wenige Ergebnisse und Gesamtanzahl)|
|`Ctrl`+`Shift`+`Space`| Einfügen von Datenzellenauswahlals als Filter in die Abfrage|
|`Ctrl`+`Space`| IntelliSense-Regeln überprüfen. Mögliche Optionen werden in jeder Regel angezeigt, die |
|`Ctrl`+`Enter`| Fügt `pipe` Ein Symbol hinzu und wechselt zu einer neuen Zeile|
|`Ctrl`+`Z`| Rückgängig |
|`Ctrl`+`Y`| Re-do |
|`Ctrl`+`L`| Löscht die aktuelle Zeile|
|`Ctrl`+`D`| Löscht die aktuelle Zeile| 
|`Ctrl`+`F`| Öffnet `Find and Replace` Dialog |
|`Ctrl`+`G`| Öffnet `Go-to line` Dialog |
|`Ctrl`+`F8` | Meine Anfragen der letzten 3 Tage anzeigen |
|`Ctrl`+ Halterung | Wenn sich der Cursor `(` `)` in `[` `]` Klammern `{` `}` befindet: , , , , - bewegt den Cursor in die passende öffnende oder schließende Klammer |
|`Ctrl`+`Shift`+`Q` | Prettify aktuelle Abfrage |
|`Ctrl`+`Shift`+`L` | Aktuelle Abfrage oder Auswahl kleinbeistellen |
|`Ctrl`+`Shift`+`U` |  Aktuelle Abfrage oder Auswahl großbuchstaben |
|`Ctrl`+`Mouse wheel up`| Erhöht die Schriftart des qupery-Editors| 
|`Ctrl`+`Mouse wheel down`| Verringert die Schriftart des Abfrageeditors|
|`Alt`+`P` | Öffnet Abfrageparameter-Dialog |
|`F2`| Aktuelle Zeile / markierten Text im Editor-Dialog öffnen |
|`Ctrl`+`F6`| Führt die statische KQL-Abfrageanalyse aus, um häufig auftretende Probleme zu erkennen |
|`F12`| Navigieren zur Definition des Symbols |
|`Ctrl`+`F12`| Alle Verweise auf das aktuelle Symbol finden |
|`Alt`+`Home`| Navigieren zur Definition des Symbols |
|`Alt`+`Ctrl`+`M`| Extrahieren sie den aktuell ausgewählten Literal- oder Tabellarischen Ausdruck als let-Anweisung |
|`Ctrl`+`.`| Extrahieren sie den aktuell ausgewählten Literal- oder Tabellarischen Ausdruck als let-Anweisung |
|`Ctrl`+`R`, `Ctrl`+`R` | Umbenennung des aktuellen Symbols |
|`Ctrl`+`K`, `Ctrl`+`D` | Fügt den aktuellen Zeitstempel als detatime-Literal ein |
|`Ctrl`+`K`, `Ctrl`+`R` | Einsätze `range x from 1 to 1 step 1` Snippet |
|`Ctrl`+`K`, `Ctrl`+`C` | Kommentieren der aktuellen Zeile oder ausgewählter Linien |
|`Ctrl`+`K`, `Ctrl`+`F` | Prettify aktuelle Abfrage |
|`Ctrl`+`K`, `Ctrl`+`V` | Aktuelle Abfrage duplizieren (an das Ende des aktuellen Abfragedokuments anhängen) |
|`Ctrl`+`K`, `Ctrl`+`U` | Abmelden der aktuellen Zeile oder ausgewählter Zeilen |
|`Ctrl`+`K`, `Ctrl`+`S` | Drehen Sie die aktuelle Linie oder ausgewählte Linien in ein mehrzeiliges Zeichenfolgenliteral |
|`Ctrl`+`K`, `Ctrl`+`M` | Entfernen sie mehrzeilige Stirng-Literalmarken (Rückseite `Ctrl` + `K`von , `Ctrl` + `S`) |
|`Ctrl`+`M`, `Ctrl`+`M` | Umschalten der Gliederungserweiterung der aktuellen Abfrage |
|`Ctrl`+`M`, `Ctrl`+`L` | Umschalten der Gliederung sumrissen Erweiterung aller Abfragen im Dokument |

## <a name="json-viewer"></a>JSON-Viewer

Die folgenden Tastatur-Shorcuts können innerhalb des Ergebnisses JSON-Viewer verwendet werden (angezeigt, wenn man auf einen JSON-ähnlichen Wert in der Ergebnisansichtszelle doppelklickt):

|Hot Key|Beschreibung|
|-------|-----------|
|`Ctrl`+`Up Arrow`|Navigieren zu übergeordneten|
|`Ctrl`+`Right Arrow`|Erweitern des aktuellen Knotens (eine Ebene)|
|`Ctrl`+`Left Arrow`|Aktueller Knoten reduzieren (eine Ebene)|
|`Ctrl`+`.`|Umschalten der Erweiterung des aktuellen Knotens (alle untergeordneten Ebenen erweitert/reduziert)
|`Ctrl`+`Shift`+`.`|Umschalten der Erweiterung des aktuellen übergeordneten Knotens (alle untergeordneten Ebenen erweitert/reduziert)|

## <a name="connection-panel"></a>Anschlusspanel

Die folgenden Tastatur-Shorcuts können innerhalb des Ergebnisses JSON-Viewer verwendet werden (angezeigt, wenn man auf einen JSON-ähnlichen Wert in der Ergebnisansichtszelle doppelklickt):

|Hot Key|Beschreibung|
|-------|-----------|
|`Ctrl`+`Up`Pfeil|Navigieren zu übergeordneten|
|`Ctrl`+`Right`Pfeil|Erweitern des aktuellen Knotens (eine Ebene)|
|`Ctrl`+`Left`Pfeil|Aktueller Knoten reduzieren (eine Ebene)|
|`Ctrl`+`Shift`+`L`|Alle Ebenen reduzieren|
|`Ctrl`+`R`|Aktualisieren der aktuell ausgewählten Verbindung|
|`Insert`|Hinzufügen einer neuen Verbindung|
|`Del`|Aktuelle Verbindung löschen|
|`Ctrl`+`E`|Bearbeiten der aktuell ausgewählten Verbindung|
|`Ctrl`+`T`|Öffnen eines neuen Abfrage-Editors unter Verwendung der aktuell ausgewählten Verbindung|

## <a name="diagnostics-and-monitoring"></a>Diagnose und Überwachung

Die folgenden Tastenkombinationen `Monitoring` sind über das Menüband verfügbar.

|Hot Key|Beschreibung|
|-----------|-----------|
|`Ctrl`+`Shift`+`F1`|Ausführen des Clusterdiagnoseflusses|