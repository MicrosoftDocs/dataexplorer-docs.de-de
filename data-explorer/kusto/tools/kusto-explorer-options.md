---
title: 'Optionen für den Kusto-Explorer: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden die Kusto-Explorer-Optionen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 5b1bb73778858998f835f1e8718afbfbd6b69443
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108387"
---
# <a name="kusto-explorer-options"></a>Optionen in Kusto-Explorer

In den folgenden Tabellen werden die Optionen zum Anpassen des Verhaltens von Kusto. **Explorer** > im Dialogfeld Extras**Optionen** beschrieben.

## <a name="general-settings"></a>Allgemeine Einstellungen

| Option | Beschreibung |
|---------|--------------|
| Toolmodus | Aktiviert Beta-, Alpha-und experimentelle Anwendungs Features. Standardwert ist „none“.|
| Erweiterter Zugriff | Wenn diese Option aktiviert ist, werden von den Barrierefreiheits Tools verwendete Barrierefreiheits Ereignisse gesendet. Kann die Leistung beeinträchtigen, wenn Sie aktiviert ist. Die Standardeinstellung ist „Deaktiviert“. |
| Senden von Telemetriedaten zulassen | Wenn diese Option aktiviert ist, sendet die Anwendung Telemetriedaten, wenn Fehler auftreten oder die Anwendung abstürzt. |
| Begrüßungsnachricht | Wenn diese Option aktiviert ist, zeigt die Anwendung die Willkommensnachricht beim Start an. Die Standardeinstellung ist „Aktiviert“.|
| Design anzeigen | Das Benutzeroberflächen Farbschema der Anwendung: hell oder dunkel.|
  
## <a name="query-editor"></a>Abfrage-Editor

| Option | Beschreibung |
|---------|--------------|
| Automatisches Speichern von Abfragen | Wenn diese Option aktiviert ist, speichert die Anwendung geöffnete Dokumente automatisch. Wenn Sie diese Einstellung ändern, muss die Anwendung neu gestartet werden, damit Sie wirksam wird. Standardmäßig aktiviert.|
| Nachverfolgen von Änderungen | Wenn diese Option aktiviert ist, werden Änderungen, die von anderen Anwendungen zum Abfragen von Skripts auf dem Datenträger vorgenommen wurden Wenn das Abfrage Skript extern geändert wird, werden Sie benachrichtigt und aufgefordert, es erneut zu laden. Wenn Sie diese Einstellung ändern, muss die Anwendung neu gestartet werden. Standardmäßig aktiviert.|
| Verwenden von Bearbeitungs Sitzungen | Wenn diese Option aktiviert ist, besitzt jeder Anwendungsprozess einen eigenen Satz von Dokumenten. Standardmäßig deaktiviert.|
| Schriftgrad | Der im Abfrage-Editor verwendete Schrift Grad.|
| Befehls Hintergrund auswählen | Die Hintergrundfarbe, die verwendet wird, um den derzeit ausgewählten Befehl hervorzuheben.|
| Ersetzen von Registerkarten durch Leerzeichen | Wenn diese Option aktiviert ist, werden Registerkarten automatisch durch Leerzeichen ersetzt.|
| Funktionsparameter Injektion deaktivieren | Wenn diese Option aktiviert ist, wird die Funktionsparameter Injektion aus der Zwischenablage deaktiviert.|
| Abfrage Parameter Injektion deaktivieren | Wenn diese Option aktiviert ist, wird die Abfrage Parameter Injektion deaktiviert.|
| Abfrage Lauf Trigger außer F5 deaktivieren | Wenn diese Option aktiviert ist, löst nur die F5-Taste die Ausführung von Abfragen aus.|
| Anzeigen der Hilfe in der Anwendung | Wenn diese Option aktiviert ist, werden in der Anwendung Hilfe Themen angezeigt. Wenn deaktivierte Hilfe Themen in einem Browser geöffnet werden.|
| Längen Limit für Abfrage Parameter | Die maximale Länge einer Zeichenfolge, die als Abfrage Parameter verwendet werden kann. Wenn Sie diesen Wert auf mehr als 128 KB festlegen, kann dies zu Leistungsproblemen führen. Der Standardwert ist 64K.|

## <a name="intellisense"></a>IntelliSense

| Option | Beschreibung |
|---------|--------------|
| IntelliSense-Version | Die verwendete Version der IntelliSense-Engine. *V1* ist die klassische Engine. *V2* ist das moderne Modul. Der Standardwert ist *v2*. |
| IntelliSense: Control-Befehle | Die Version von IntelliSense für Steuerungsbefehle. *V1* ist die klassische Engine. *V2* ist das moderne Modul. Der Standardwert ist *v2*. | 
| IntelliSense | Aktiviert oder deaktiviert IntelliSense. Die Standardeinstellung ist „Aktiviert“.|
| Syntax Hervorhebung | Aktiviert oder deaktiviert die Syntax Hervorhebung. Die Standardeinstellung ist „Aktiviert“.|
| Tooltipps | Aktiviert oder deaktiviert die QuickInfo, die angezeigt werden, wenn mit dem Mauszeiger auf Bereiche der ausgewählten Abfrage gezeigt wird. Die Standardeinstellung ist „Aktiviert“.|

## <a name="formatter"></a>Formatierungsprogramm

| Option | Beschreibung |
|---------|--------------|
| Version | Die Version des Formatierers, die verwendet wird, wenn das prettify Query-Tool auf die aktuelle Abfrage angewendet wird. *V1* ist das klassische Formatierer. *V2* ist das moderne Formatierer. Der Standardwert ist *v2*.|
| Indentation | Die Anzahl der Leerzeichen für Elemente mit Einzügen.|
| Funktions Klammern | Die Platzierung von Funktions geschweiften Klammern. *Vertikale* stellen öffnen und schließen geschweifte Klammern in neuen Zeilen. *Horizontal* verlässt die öffnende geschweifter Klammer in der ursprünglichen Zeile und platziert die schließende geschweifter Klammer in einer neuen Zeile. *None* lässt alle geschweiften Klammern unverändert.|
| Pipe-Operator | Die Platzierung der Pipe (Balken Zeichen) zwischen tabellarischen Abfrage Operatoren. In der *neuen Zeile* werden alle Pipezeichen in einer neuen Zeile platziert. *Smart* platziert alle Pipezeichen in einer neuen Zeile, wenn die Abfrage bereits mehr als eine Zeile umfasst. *None* lässt alle Pipezeichen unverändert.|
| Semicolon | Die Platzierung von Semikolons, die Abfrage Anweisungen beenden. In der *neuen Zeile* wird ein Semikolon in einer neuen Zeile eingefügt. *Smart* platziert Semikolons in einer neuen Zeile, wenn die Anweisung selbst mehr als eine Zeile umfasst.  " *None* " verlässt Semikolons unverändert.
| Fehlende Syntax einfügen | Fügt fehlende Interpunktions Zeichen wie (z. b. Kommas und Semikolons) hinzu, für die Sie Fehlermeldungen erhalten.|

## <a name="results-viewer"></a>Ergebnisanzeige

| Option | Beschreibung |
|---------|--------------|
| Schriftgrad | Der vom Datentabellen Raster verwendete Schrift Grad, in dem Abfrageergebnisse angezeigt werden.|
| Ausführlichkeits Farbschema | Wählt das Farbschema für die Zeilen Formatierung basierend auf dem automatisch erkannten ausführlichkeits Grad aus.|
| Leere Spalten ausblenden | Wenn diese Option aktiviert ist, werden leere Spalten in der Sicht, die Daten anzeigen, ausgeblendet.  Die Standardeinstellung ist „Deaktiviert“.|
| Reduzieren von Spalten mit einem Wert| Wenn diese Option aktiviert ist, werden einzelne Wert Spalten in der Sicht, in der Daten angezeigt werden, automatisch reduziert. Spalten mit einzelnen nicht leeren Werten werden als Gruppen angezeigt. Die Standardeinstellung ist „Deaktiviert“.|
| Ergebnisse automatisch nach datetime-Spalten sortieren | Wenn diese Option aktiviert ist, werden die Zeilen automatisch nach datetime-Spalten sortiert. Die Standardeinstellung ist „Aktiviert“.|
| Sichtbarer Bereich der Spalte | Die maximale Anzahl von Zeichen, die in einer Spalte angezeigt werden sollen. Der Standardwert ist 2048.|
| JSON als Text visualisieren | Wenn diese Option aktiviert ist, werden die JSON-Werte als Text visualisiert. Die Standardeinstellung ist „Deaktiviert“.|
| Maximale Größe von Text Details | Die maximale Anzahl von Zeichen, die im detaillierten Informationsbereich angezeigt werden, wenn auf die Zelle Doppel geklickt wird. Der Standardwert ist 32 KB.|

## <a name="connections"></a>Verbindungen

| Option | Beschreibung |
|---------|--------------|
| Tabellen Spalten alphabetisch sortieren | Wenn diese Option aktiviert ist, werden die Spalten, die unter den Tabellen Knoten im Verbindungs Panel angezeigt werden, alphabetisch sortiert.|
| Timeout für Abfrage Server | Das Server Timeout für die Abfrage Ausführung.|
| Warnung bei Verbindungs Sperre | Wenn diese Option aktiviert ist, wird die Verbindungs Sperre bei jedem Verbindungswechsel bei einem Verbindungswechsel von der Anwendung gewarnt. Die Standardeinstellung ist „Aktiviert“.|
| Verzögerte Schema Untersuchung | Wenn diese Option aktiviert ist, ruft der Bereich Verbindungen nur das Datenbankschema ab, wenn der Datenbankknoten erweitert wird.|
| Ausgeblendete Systemobjekte anzeigen | Wenn diese Option aktiviert ist, werden verborgene Systemobjekte angezeigt, wenn der Benutzer über die entsprechenden Berechtigungen verfügt.|
| Schwache Konsistenz der Abfrage | Wenn diese Option aktiviert ist, wird die schwache Konsistenz für Abfragen verwendet.|
| Kusto-Parser-Version | Die Version des Parsers, der zum Ausführen von Abfragen verwendet wird. *V1* ist der klassische Parser. *V2* ist der moderne Parser. Der Standardwert ist *v1*.|

## <a name="diagnostic-tracing"></a>Diagnose Ablauf Verfolgung

| Option | Beschreibung |
|---------|--------------|
| Aktivieren der Ablaufverfolgung | Aktiviert die Ablaufverfolgung|
| Ausführlichkeit der Ablauf Verfolgung | Legt die Ausführlichkeit der Ablauf Verfolgung fest.|
| Ablauf Verfolgungs Speicherort | Der Speicherort, an dem Ablauf Verfolgungen protokolliert werden.|
| Platformtraceverbosity | Legt den ausführlichkeits Grad der Ablauf Verfolgung für die Plattform fest.| 