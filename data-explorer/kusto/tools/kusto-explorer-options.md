---
title: Kusto Explorer-Optionen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden die Kusto Explorer-Optionen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: dee96dd937b4258525673ced10f206836f6e51c2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524003"
---
# <a name="kusto-explorer-options"></a>Kusto Explorer-Optionen

In den folgenden Tabellen werden die Optionen zum Anpassen des Verhaltens von Kusto.Explorer im Dialogfeld > **Extraoptionen** beschrieben. **Tools**

![Tools** > **Optionen**](\images\Kusto-Tools-Explorer-Options\tools-options.png)

## <a name="general-settings"></a>Allgemeine Einstellungen

| Option | Beschreibung |
|---------|--------------|
| Werkzeugmodus | Aktiviert Beta-, Alpha- und experimentelle Anwendungsfunktionen. Standardwert ist „none“.|
| Erweiterte Barrierefreiheit | Wenn diese Option aktiviert ist, sendet die Anwendung Eingabehilfenereignisse, die von Eingabehilfen verwendet werden. Kann sich auf die Leistung auswirken, wenn diese Option aktiviert ist. Der Standardwert ist deaktiviert. |
| Senden von Telemetriedaten zulassen | Wenn diese Option aktiviert ist, sendet die Anwendung Telemetriedaten, wenn Fehler auftreten oder die Anwendung abstürzt. |
| Begrüßungsnachricht | Wenn diese Option aktiviert ist, wird die Begrüßungsnachricht beim Start angezeigt. Der Standardwert ist aktiviert.|
| Display-Thema | Das Anwendungs-UI-Farbschema: hell oder dunkel.|
  
## <a name="query-editor"></a>Abfrage-Editor

| Option | Beschreibung |
|---------|--------------|
| Abfragen automatisch speichern | Wenn diese Option aktiviert ist, speichert die Anwendung automatisch geöffnete Dokumente. Um diese Einstellung zu ändern, muss die Anwendung neu gestartet werden, damit sie wirksam wird. Standardmäßig aktiviert.|
| Nachverfolgen von Änderungen | Wenn diese Option aktiviert ist, verfolgt die Anwendung Änderungen, die von anderen Anwendungen an Abfrageskripts auf dem Datenträger vorgenommen wurden. Wenn das Abfrageskript extern geändert wird, werden Sie benachrichtigt und zum erneuten Laden aufgefordert. Um diese Einstellung zu ändern, muss die Anwendung neu gestartet werden. Standardmäßig aktiviert.|
| Verwenden von Bearbeitungssitzungen | Wenn diese Option aktiviert ist, besitzt jeder Anwendungsprozess einen eigenen Satz von Dokumenten. Standardmäßig deaktiviert.|
| Schriftgrad | Die im Abfrageeditor verwendete Schriftgröße.|
| Auswählen des Befehlshintergrunds | Die Hintergrundfarbe, die zum Hervorheben des aktuell ausgewählten Befehls verwendet wird.|
| Ersetzen von Registerkarten durch Leerzeichen | Wenn diese Option aktiviert ist, werden Registerkarten automatisch durch Leerzeichen ersetzt.|
| Deaktivieren der Funktionsparameterinjektion | Wenn diese Option aktiviert ist, ist die Einspritzung des Funktionsparameters aus der Zwischenablage deaktiviert.|
| Deaktivieren der Abfrageparameterinjektion | Wenn diese Option aktiviert ist, ist die Abfrageparameterinjektion deaktiviert.|
| Deaktivieren von Abfrageausführungstriggern außer F5 | Wenn diese Option aktiviert ist, löst nur der F5-Schlüssel die Ausführung von Abfragen aus.|
| Hilfe innerhalb der Anwendung anzeigen | Wenn diese Option aktiviert ist, werden Hilfethemen in der Anwendung angezeigt. Wenn diese Option deaktiviert ist, werden Hilfethemen in einem Browser geöffnet.|
| Längenbeschränkung für Abfrageparameter | Die maximale Länge einer Zeichenfolge, die als Abfrageparameter verwendet werden kann. Das Festlegen dieses Werts auf mehr als 128 K kann zu Leistungsproblemen führen. Der Standardwert ist 64K.|

## <a name="intellisense"></a>IntelliSense

| Option | Beschreibung |
|---------|--------------|
| IntelliSense-Version | Die verwendete Version der IntelliSense-Engine. *V1* ist der klassische Motor. *V2* ist der moderne Motor. Der Standardwert ist *V2*. |
| IntelliSense: Steuerbefehle | Die Version von IntelliSense für Steuerbefehle. *V1* ist der klassische Motor. *V2* ist der moderne Motor. Der Standardwert ist *V2*. | 
| IntelliSense | Aktiviert oder deaktiviert IntelliSense. Der Standardwert ist aktiviert.|
| Syntaxhervorhebung | Aktiviert oder deaktiviert die Syntaxhervorhebung. Der Standardwert ist aktiviert.|
| Tool-Tipps | Aktiviert oder deaktiviert die Tool-Tipps, die angezeigt werden, wenn die Maus über Bereiche der ausgewählten Abfrage schwebt. Der Standardwert ist aktiviert.|

## <a name="formatter"></a>Formatierungsprogramm

| Option | BESCHREIBUNG |
|---------|--------------|
| Version | Die Version des Verfilmungsteils, die verwendet wird, wenn das Werkzeug Prettify Query auf die aktuelle Abfrage angewendet wird. *V1* ist der klassische Formatter. *V2* ist der moderne Formant. Der Standardwert ist *V2*.|
| Indentation | Die Anzahl der Leerzeichen für eingerückte Elemente.|
| Funktionsklammern | Die Platzierung von Funktionsgeschweiern. *Vertikale* Stellen öffnen und schließen geschweifte Klammern auf neuen Linien. *Horizontal* lässt die offene Klammer auf ihrer ursprünglichen Linie und platziert die schließende Klammer auf einer neuen Linie. *Keiner* hinterlässt alle Geschweifungen so, wie sie sind.|
| Rohroperator | Die Platzierung des Rohrzeichens (Bar) zwischen tabellarischen Abfrageoperatoren. *Neue Zeile* platziert alle Rohrzeichen in einer neuen Zeile. *Smart* platziert alle Rohrzeichen in einer neuen Zeile, wenn die Abfrage bereits mehr als eine Zeile umfasst. *Keiner* hinterlässt alle Rohrzeichen so, wie es ist.|
| Semicolon | Die Platzierung von Semikolons, die Abfrageanweisungen beenden. *Neue Linie* platziert Semikolons auf einer neuen Linie, eingerückt. *Smart* platziert Semikolons in einer neuen Zeile, wenn die Anweisung selbst mehr als eine Zeile umfasst.  *Keiner* hinterlässt Semikolons, wie es ist.
| Fehlende Syntax einfügen | Fügt fehlende Interpunktion wie (z. B. Kommas und Semikolons) hinzu, für die Fehlermeldungen angezeigt werden.|

## <a name="results-viewer"></a>Ergebnisanzeige

| Option | Beschreibung |
|---------|--------------|
| Schriftgrad | Die vom Datentabellenraster verwendete Schriftgröße, in der Abfrageergebnisse angezeigt werden.|
| Ausführliches Farbschema | Wählt das Farbschema für die Zeilenformatierungsbasis auf der ebene der automatisch erkannten Ausführlichkeit aus.|
| Leere Spalten ausblenden | Wenn diese Option aktiviert ist, werden leere Spalten in der Ansicht mit Daten ausgeblendet.  Der Standardwert ist deaktiviert.|
| Reduzieren von Einwertspalten| Wenn diese Option aktiviert ist, werden einwertige Spalten in der Ansicht, in der Daten angezeigt werden, automatisch reduziert. Spalten mit einzelnen nicht leeren Werten werden als Gruppen angezeigt. Der Standardwert ist deaktiviert.|
| Automatisches Sortieren von Ergebnissen nach Datetime-Spalten | Wenn diese Option aktiviert ist, werden Zeilen automatisch nach Datetime-Spalten sortiert. Der Standardwert ist aktiviert.|
| Sichtbarer Spaltenbereich | Die maximale Anzahl von Zeichen, die in einer Spalte angezeigt werden sollen. Der Standardwert ist 2048.|
| Visualisieren von JSON als Text | Wenn diese Option aktiviert ist, werden JSON-Werte als Text visualisiert. Der Standardwert ist deaktiviert.|
| Maximale Textdetails Größe | Die maximale Anzahl von Zeichen, die im detaillierten Informationsfenster angezeigt werden, wenn auf die Zelle doppelgeklickt wird. Der Standardwert ist 32 KB.|

## <a name="connections"></a>Verbindungen

| Option | Beschreibung |
|---------|--------------|
| Tabellenspalten alphabetisch sortieren | Wenn diese Option aktiviert ist, werden die Spalten, die unter den Tabellenknoten im Verbindungsbedienfeld angezeigt werden, alphabetisch sortiert.|
| Abfrageservertimeout | Das Servertimeout für die Abfrageausführung.|
| Warnung vor Verbindungssperre | Wenn diese Option aktiviert ist, warnt sie bei jedem Versuch eines Verbindungsschalters vor der Verbindungssperre. Der Standardwert ist aktiviert.|
| Lazy Schema Exploration | Wenn diese Option aktiviert ist, ruft das Verbindungsbedienfeld das Datenbankschema nur ab und zeigt es an, wenn der Datenbankknoten erweitert wird.|
| Ausgeblendete Systemobjekte anzeigen | Wenn diese Option aktiviert ist, werden ausgeblendete Systemobjekte angezeigt, wenn der Benutzer über die entsprechenden Berechtigungen verfügt.|
| Abfrage schwache Konsistenz | Wenn diese Option aktiviert ist, wird eine schwache Konsistenz für Abfragen verwendet.|
| Kusto-Parser-Version | Die Version des Parsers, die zum Ausführen von Abfragen verwendet wird. *V1* ist der klassische Parser. *V2* ist der moderne Parser. Der Standardwert ist *V1*.|

## <a name="diagnostic-tracing"></a>Diagnose-Ablaufverfolgung

| Option | Beschreibung |
|---------|--------------|
| Aktivieren der Ablaufverfolgung | Aktiviert die Ablaufverfolgung|
| Spurausführlichkeit | Legt die Ausführlichkeit der Ablaufverfolgung fest.|
| Spurenposition | Der Speicherort, an dem Ablaufverfolgungen protokolliert werden.|
| PlatformTraceVerbosity | Legt die Ausführlichkeit der Ablaufverfolgung für die Plattform fest.| 