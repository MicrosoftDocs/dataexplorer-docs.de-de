---
title: 'Kusto-CLI: Azure-Daten-Explorer'
description: In diesem Artikel wird die Kusto-CLI in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: adc606c787ab20f228a9fbd132d1b82660aa57c6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512485"
---
# <a name="kusto-cli"></a>Kusto-CLI

Kusto. CLI ist ein Befehlszeilen-Hilfsprogramm, das zum Senden von Anforderungen an Kusto und zum Anzeigen der Ergebnisse verwendet wird. Sie kann in einem von mehreren Modi ausgeführt werden:

* *Repl-Modus*: der Benutzer gibt Abfragen und Befehle ein, und das Tool zeigt die Ergebnisse an und wartet dann auf den nächsten Benutzer Abfrage/-Befehl.
  ("Repl" steht für "Read/eval/Print/Loop".)

* *Ausführungs Modus*: der Benutzer gibt eine oder mehrere Abfragen und Befehle ein, die als Befehlszeilenargumente ausgeführt werden. Die Argumente werden automatisch nacheinander ausgeführt, und die Ergebnisse werden in der Konsole ausgegeben. Wenn alle Eingabe Abfragen und Befehle ausgeführt wurden, wechselt das Tool optional in den repl-Modus.

* *Skript Modus*: vergleichbar mit dem Ausführungs Modus, aber mit den Abfragen und Befehlen, die über eine Skriptdatei angegeben werden.

Kusto. CLI wird hauptsächlich für die Automatisierung von Aufgaben für einen Kusto-Dienst bereitgestellt, der normalerweise das Schreiben von Code erfordert. Beispielsweise ein c#-Programm oder ein PowerShell-Skript.

## <a name="get-the-tool"></a>Tool herunterladen

Kusto. CLI ist Teil des nuget-Pakets `Microsoft.Azure.Kusto.Tools` , das [Sie herunterladen können](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
Nachdem Sie das Paket heruntergeladen haben, extrahieren Sie den Ordner des Pakets `tools` in den Zielordner. Es ist keine zusätzliche Installation erforderlich, da es sich um eine XCOPY-Installable handelt.

## <a name="run-the-tool"></a>Ausführen des Tools

Kusto. CLI erfordert mindestens ein Befehlszeilenargument, um ausgeführt werden zu können. Normalerweise ist dieses Argument die Verbindungs Zeichenfolge für den Kusto-Dienst, mit dem das Tool eine Verbindung herstellen soll.
Weitere Informationen finden Sie unter [Kusto-Verbindungs](../api/connection-strings/kusto.md)Zeichenfolgen. Wenn Sie das Tool ohne Befehlszeilenargumente, mit einem unbekannten Satz von Argumenten oder mit dem `/help` Schalter ausführen, wird eine Hilfe Meldung in der Konsole angezeigt.

Verwenden Sie z. b. den folgenden Befehl, um Kusto. CLI auszuführen. Mit dem Befehl wird eine Verbindung mit dem `help` Kusto-Dienst hergestellt, und der Daten Bank Kontext wird auf die Datenbank festgelegt `Samples` :

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> Verwenden Sie doppelte Anführungszeichen um die Verbindungs Zeichenfolge, um zu verhindern, dass Shellanwendungen, z. b. PowerShell, das Semikolon ( `;` ) und ähnliche Zeichen falsch interpretieren.

## <a name="command-line-arguments"></a>Befehlszeilenargumente

`Kusto.Cli.exe`*ConnectionString* [*Switches*]

*ConnectionString*
* Die [Kusto-Verbindungs Zeichenfolge](../api/connection-strings/kusto.md) , die alle Kusto-Verbindungsinformationen enthält.
  Der Standardwert lautet `net.tcp://localhost/NetDefaultDB`.

`-execute:`*Queryorcommand*
* Wenn angegeben, wird die Datei "Kusto. CLI" im Ausführungs Modus ausgeführt, und die angegebene Abfrage oder der angegebene Befehl wird ausgeführt. Dieser Schalter kann wiederholt werden, und die Abfragen/Befehle werden sequenziell in der Reihenfolge der Darstellung ausgeführt.
  Dieser Switch kann nicht in Verbindung mit `-script` oder verwendet werden `-scriptml` .

`-keepRunning:`*Enablekeeprunning*
* Wenn als oder angegeben, wird der `true` `false` repl-Modus aktiviert oder deaktiviert, nachdem alle- `-script` oder- `-execute` Werte verarbeitet wurden.

`-script:`*ScriptFile*
* Wenn angegeben, wird die Befehlszeilenschnittstelle im Skript Modus ausgeführt. Die angegebene Skriptdatei wird geladen, und die Abfragen oder Befehle in der Datei werden nacheinander ausgeführt.
  Zeilenumbrüche werden verwendet, um Abfragen/Befehle zu begrenzen, außer wenn Zeilen mit `&` einer `&&` Kombination aus oder enden, wie unten erläutert.
  Dieser Switch kann nicht in Verbindung mit verwendet werden `-execute` .

`-scriptml:`*ScriptFile*
* Wenn angegeben, wird die Befehlszeilenschnittstelle im Skript Modus ausgeführt. Die angegebene Skriptdatei wird geladen, und die Abfragen oder Befehle in der Datei werden nacheinander ausgeführt.
  Die gesamte Skriptdatei wird als einzelne Abfrage oder Befehl behandelt.
  Dieser Switch kann nicht in Verbindung mit verwendet werden `-execute` .

`-echo:`*Enableechomode*
* Wenn als oder angegeben, wird der `true` `false` Echo Modus aktiviert oder deaktiviert.
  Wenn der Echo-Modus aktiviert ist, wird jede Abfrage bzw. jeder Befehl in der Ausgabe wiederholt.

`-transcript:`*"Transcriptfile"*  
* Wenn angegeben, wird die Programmausgabe in *transcriptfile*geschrieben.

`-logToConsole:`*Enablelogdeconsole*
* Wenn angegeben, entweder als `true` oder `false` , wird die Anzeige der Programmausgabe in der Konsole aktiviert oder deaktiviert.

`-lineMode:`*Enablelinemode*
* Wenn angegeben, wechselt zwischen dem Standardzeilen Eingabemodus, wenn auf festgelegt, `true` und dem Block Eingabemodus, wenn auf festgelegt `false` . Im folgenden finden Sie eine Erläuterung dieser beiden Modi, die bestimmen, wie Zeilenumbrüche behandelt werden.

**Beispiel**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

> [!NOTE] 
> Zwischen dem Doppelpunkt und dem Argument Wert darf kein Leerzeichen stehen.

## <a name="directives"></a>Anweisungen

"Kusto. CLI" führt eine Reihe von Direktiven im Tool aus, anstatt Sie zur Verarbeitung an den Dienst zu senden.

|Anweisung                      |Beschreibung|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |Kurze Hilfe Nachricht erhalten|
|`q`<br>`#quit`<br>`#exit`      |Tool beenden|
|`#a`<br>`#abort`               |Beenden Sie das Tool abgebrochen|
|`#clip`                        |Die Ergebnisse der nächsten Abfrage oder des nächsten Befehls werden in die Zwischenablage kopiert.|
|`#cls`                         |Konsolenbildschirm löschen|
|`#connect`*[ConnectionString*]|Stellt eine Verbindung mit einem anderen Kusto-Dienst her (wenn " *ConnectionString* " weggelassen wird, wird der aktuelle angezeigt)|
|`#crp`[*Name* [ `=` *Wert*]]   |Legt den Wert einer Client Anforderungs Eigenschaft fest oder zeigt ihn einfach an oder zeigt alle Werte an.|
|`#crp`( `-list` \| `-doc` ) [*Präfix*]|Listet Client Anforderungs Eigenschaften, Präfix oder alle|
|`#dbcontext`[*DatabaseName*]  |Ändert die von Abfragen und Befehlen verwendete Kontext Datenbank in *DatabaseName*. Wenn dieser nicht weggelassen wird, wird der aktuelle Kontext angezeigt.|
|`ke`*Text*                    |Sendet den angegebenen Text an einen laufenden Kusto. Explorer-Prozess.|
|`#loop`*Count* *Text* zählen         |Führt den Text mehrmals aus.|
|`#qp`[*Name* [ `=` *Wert*]]   |Legt den Wert eines Abfrage Parameters fest oder zeigt ihn einfach an oder zeigt alle Werte an. Einfache und doppelte Anführungszeichen am Anfang/Ende werden gekürzt.|
|`#save`*Dateiname*             |Die Ergebnisse der nächsten Abfrage oder des nächsten Befehls werden in der angegeben CSV-Datei gespeichert.|
|`#script`*Dateiname*           |Führt das gekennzeichnete Skript aus.|
|`#scriptml`*Dateiname*         |Führt das mehrzeilige Skript aus.|

## <a name="line-input-mode-and-block-input-mode"></a>Zeilen Eingabemodus und Block Eingabemodus

Standardmäßig wird "Kusto. CLI" im **Zeilen Eingabemodus**ausgeführt. Jedes neue Zeilen Trennzeichen wird als Trennzeichen zwischen Abfragen/Befehlen interpretiert, und die Zeile wird sofort zur Ausführung gesendet.

In diesem Modus können Sie eine lange Abfrage oder einen Befehl in mehrere Zeilen unterbrechen. Das `&` Zeichen als letztes Zeichen einer Zeile vor dem Zeilen Vorstrich bewirkt, dass Kusto. CLI das Lesen der nächsten Zeile fortsetzt. Das `&&` Zeichen als letztes Zeichen einer Zeile vor dem Zeilen Vorschritt bewirkt, dass Kusto. CLI den Zeilen Vorstrich ignoriert und das Lesen der nächsten Zeile fortsetzt.

Kusto. CLI unterstützt auch die Ausführung im **Block Eingabemodus**. Wenn Sie entweder den Befehls Zeilenschalter `-lineMode:false` oder die-Direktive verwenden `#blockmode` , können Sie Kusto. CLI anweisen, zu übernehmen, dass jede Zeile eine Fortsetzung der vorherigen Zeile ist, sodass Abfragen und Befehle nur durch eine leere Eingabezeile getrennt werden.

## <a name="comments"></a>Kommentare

Kusto. CLI interpretiert eine `//` Zeichenfolge, die eine neue Zeile startet, als Kommentarzeile. Der Rest der Zeile wird ignoriert, und das Lesen der nächsten Zeile wird fortgesetzt.

## <a name="tool-only-options"></a>Tool-only-Optionen

Befehle                        | Effekt                                                                            | Aktuell
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | aktivieren/deaktivieren (Option) `timing` : Anzeigen der benötigte Zeitanforderungen                    | TRUE
#tableon|#tableoff              | aktivieren/deaktivieren (Option) `tableView` : Formatieren von Resultsets als Tabellen                  | TRUE
#marson|#marsoff                | aktivieren/deaktivieren (Option) `marsView` : Anzeigen der Second-to-Last-Resultsets          | FALSE
#resultson|#resultsoff          | aktivieren/deaktivieren (Option) `outputResultsSet` : Anzeigen der Resultsets                 | TRUE
#prettyon|#prettyoff            | Option "aktivieren/deaktivieren" `prettyErrors` : Bereinigungs Fehler                             | TRUE
#markdownon|#markdownoff        | aktivieren/deaktivieren (Option) `markdownView` : Formatieren von Tabellen als markdown                   | FALSE
#progressiveon|#progressiveoff  | aktivieren/deaktivieren (Option) `progressiveView` : anfordern und anzeigen progressiver Ergebnisse  | FALSE
#linemode|#blockmode            | aktivieren/deaktivieren (Option) `lineMode` : einzeiligen Eingabemodus                          | TRUE

Befehle                              | Effekt                                                                                                         | Standard
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (aktivieren|Option deaktivieren `crid` : zeigt die clientrequestid vor dem Senden der Anforderung an.)                          | FALSE
#csvheaderson|#csvheadersoff          | (aktivieren|Option deaktivieren `csvHeaders` : einschließen von Headern in die CSV-Ausgabe)                                            | TRUE
#focuson|#focusoff                    | (aktivieren|Option deaktivieren `focus` : Entfernen Sie alle zusätzlichen zielgerichtete, und konzentrieren Sie sich auf die richtigen Dinge.)                        | FALSE
#linemode|#blockmode                  | (aktivieren|Option deaktivieren `lineMode` : einzeiligen Eingabemodus)                                                      | TRUE
#markdownon|#markdownoff              | (aktivieren|Option deaktivieren `markdownView` : Tabellen als markdown formatieren)                                              | FALSE
#marson|#marsoff                      | (aktivieren|Deaktivieren (Option `marsView` ): Anzeigen der Second-to-Last-Resultsets                                      | FALSE
#prettyon|#prettyoff                  | (aktivieren|Option deaktivieren `prettyErrors` : Bereinigungs Fehler)                                                        | TRUE
#querystreamingon|#querystreamingoff  | (aktivieren|Option deaktivieren `queryStreaming` : Verwenden Sie den querystreaming-Endpunkt (nur Kusto-Team)).                    | FALSE
#resultson|#resultsoff                | (aktivieren|Option deaktivieren `outputResultsSet` : Anzeigen der Resultsets)                                            | TRUE
#tableon|#tableoff                    | (aktivieren|Option deaktivieren `tableView` : Formatieren von Resultsets als Tabellen                                              | TRUE
#timeon|#timeoff                      | (aktivieren|Option deaktivieren `timing` : zeigt die Zeitspanne an, die die Anforderungen benötigt haben)                               | TRUE
#typeon|#typeoff                      | (aktivieren|Option deaktivieren `typeView` : zeigt den Typ der einzelnen Spalten in der Tabellenansicht an. Erzwingt Streaming = true)| TRUE
#v2protocolon|#v2protocoloff          | (aktivieren|Option deaktivieren `v2protocol` : Verwenden Sie das v2-Abfrageprotokoll, nicht v1).                                        | TRUE

## <a name="use-kustocli-to-export-results-as-csv"></a>Verwenden von Kusto. CLI zum Exportieren von Ergebnissen als CSV

Kusto. CLI verfügt über einen speziellen Client seitigen Befehl, `#save` der die **nächsten** Abfrageergebnisse in eine lokale Datei im CSV-Format exportiert. Beispielsweise werden in der folgenden Zeile 10 Datensätze aus der `StormEvents` Tabelle in den Cluster "Database" exportiert `help.kusto.windows.net` `Samples` :

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="use-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Verwenden von Kusto. CLI zum Steuern einer laufenden Instanz von Kusto. Explorer

Sie können Kusto. CLI anweisen, mit der "primären" Instanz von Kusto. Explorer zu kommunizieren, die auf dem Computer ausgeführt wird, und IT-Abfragen senden. Dieser Mechanismus kann bei Programmen nützlich sein, die eine Reihe von Abfragen ausführen möchten, aber nicht wiederholt den Kusto. Explorer-Prozess starten möchten. Im folgenden Beispiel wird die Datei "Kusto. CLI" verwendet, um eine Abfrage für den Hilfe Cluster auszuführen:

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

Die Syntax ist einfach: `#ke` , gefolgt von einem Leerzeichen und der Abfrage, die ausgeführt werden soll.
Die Abfrage wird dann an die primäre Instanz von Kusto. Explorer gesendet, sofern vorhanden, wobei der aktuelle Cluster bzw. die aktuelle Datenbank in Kusto. CLI festgelegt ist.
 
