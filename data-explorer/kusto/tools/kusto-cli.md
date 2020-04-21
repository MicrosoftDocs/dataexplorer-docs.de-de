---
title: Kusto CLI - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Kusto CLI in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7443ad32b78a48f6b35be4f4b680ac6c728aedd2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524241"
---
# <a name="kusto-cli"></a>Kusto CLI

Kusto.Cli ist ein Befehlszeilendienstprogramm, das verwendet werden kann, um Anfragen an Kusto zu senden und deren Ergebnisse anzuzeigen. Kusto.Cli kann in einem von mehreren Modi ausgeführt werden:

* *REPL-Modus*: Der Benutzer gibt Abfragen und Befehle ein, die für Kusto ausgeführt werden sollen, und das Tool zeigt die Ergebnisse an und wartet dann auf die nächste Benutzerabfrage/den nächsten Befehl.
  ("REPL" steht für "read/eval/print/loop".)

* *Ausführungsmodus*: Der Benutzer stellt eine oder mehrere Abfragen und Befehle bereit, die als Befehlszeilenargumente für das Tool ausgeführt werden sollen. Diese werden automatisch nacheinander ausgeführt und ihre Ergebnisse werden an die Konsole ausgegeben. Optional, nachdem alle Eingabeabfragen und Befehle ausgeführt werden, wechselt das Tool in den REPL-Modus.

* *Skriptmodus*: Ähnlich wie im Ausführungsmodus, jedoch mit den Abfragen und Befehlen, die durch eine Datei angegeben werden (als "Skript" bezeichnet).

Kusto.Cli wird in erster Linie für die Automatisierung von Aufgaben für einen Kusto-Dienst bereitgestellt, der normalerweise Code schreiben müsste (z. B. ein C-Programm oder ein PowerShell-Skript).

## <a name="getting-the-tool"></a>Abrufen des Tools

Kusto.Cli ist Teil des `Microsoft.Azure.Kusto.Tools`NuGet-Pakets , das [hier](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)heruntergeladen werden kann.
Nach dem Herunterladen kann `tools` der Ordner des Pakets in den Zielordner extrahiert werden. Es ist keine zusätzliche Installation erforderlich (d.h. sie ist xcopy-installable).



## <a name="running-the-tool"></a>Ausführen des Tools

Kusto.Cli erfordert mindestens ein Befehlszeilenargument, um ausgeführt zu werden. Normalerweise ist dieses Argument die Verbindungszeichenfolge zum Kusto-Dienst, mit der das Tool eine Verbindung herstellen soll. siehe [Kusto-Verbindungszeichenfolgen](../api/connection-strings/kusto.md). Das Ausführen des Tools ohne Befehlszeilenargumente oder mit einem `/help` unbekannten Satz von Argumenten oder mit dem Switch führt dazu, dass eine Hilfemeldung an die Konsole gesendet wird.

Verwenden Sie beispielsweise den folgenden Befehl, um Kusto.Cli auszuführen und eine Verbindung mit dem `help` Kusto-Dienst herzustellen, und legen Sie den Datenbankkontext auf die `Samples` Datenbank fest:

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> Wir haben Anführungszeichen um die Verbindungszeichenfolge verwendet, um zu verhindern,`;`dass Shellanwendungen wie PowerShell das Semikolon ( ) und ähnliche Zeichen in der Verbindungszeichenfolge interpretieren.

## <a name="command-line-arguments"></a>Befehlszeilenargumente

`Kusto.Cli.exe`*ConnectionString* [*Schalter*]

*Connectionstring*
* Die [Kusto-Verbindungszeichenfolge,](../api/connection-strings/kusto.md) die alle Kusto-Verbindungsinformationen enthält.
  Der Standardwert lautet `net.tcp://localhost/NetDefaultDB`.

`-execute:`*QueryOrCommand*
* Wenn angegeben, wird Kusto.Cli im Ausführungsmodus ausgeführt; die angegebene Abfrage oder der angegebene Befehl wird ausgeführt. Dieser Schalter kann wiederholt werden, in diesem Fall werden die Abfragen/Befehle sequenziell in der Reihenfolge der Darstellung ausgeführt.
  Dieser Schalter kann nicht `-script` `-scriptml`zusammen mit oder verwendet werden.

`-keepRunning:`*EnableKeepRunning*
* Wenn `true` angegeben (entweder `false`oder ), wird der Wechsel in `-script` `-execute` den REPL-Modus aktiviert oder deaktiviert, nachdem alle Werte verarbeitet wurden.

`-script:`*ScriptFile*
* Wenn angegeben, wird Kusto.Cli im Skriptmodus ausgeführt; Die angegebene Skriptdatei wird geladen, und die darin enthaltenen Abfragen oder Befehle werden sequenziell ausgeführt.
  Zeilenlinien werden verwendet, um Abfragen/Befehle zu `&` begrenzen, es sei denn, Zeilen enden mit oder `&&` Kombinationen, wie unten erläutert.
  Dieser Schalter kann nicht `-execute`zusammen mit verwendet werden.

`-scriptml:`*ScriptFile*
* Wenn angegeben, wird Kusto.Cli im Skriptmodus ausgeführt; Die angegebene Skriptdatei wird geladen, und die darin enthaltenen Abfragen oder Befehle werden sequenziell ausgeführt.
  Die gesamte Skriptdatei wird als einzelne Abfrage oder befehl betrachtet.
  Dieser Schalter kann nicht `-execute`zusammen mit verwendet werden.

`-echo:`*EnableEchoMode*
* Wenn angegeben (als entweder `true` oder `false`), aktiviert oder deaktiviert Echo-Modus.
  Wenn der Echomodus aktiviert ist, wird jede Abfrage oder jeder Befehl in der Ausgabe wiederholt.

`-transcript:`*TranscriptFile*  
* Wenn angegeben, schreibt die Programmausgabe in *TranscriptFile*.

`-logToConsole:`*EnableLogToConsole*
* Wenn angegeben `true` (entweder `false`oder ), wird das Schreiben der Programmausgabe an die Konsole aktiviert oder deaktiviert.

`-lineMode:`*EnableLineMode*
* Wenn angegeben, wechselt zwischen dem Zeileneingabemodus (Standardmodus oder wenn auf ) und `true`dem Blockeingangsmodus (wenn auf `false`gesetzt). Siehe unten für eine Erklärung dieser beiden Modi, die bestimmen, wie Neulinien behandelt werden.

**Beispiel**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

Bitte beachten Sie, dass zwischen dem Doppelpunkt und dem Argumentwert kein Leerzeichen vorhanden sein sollte.

## <a name="directives"></a>Anweisungen

Kusto.Cli unterstützt eine Reihe von Direktiven, die im Tool ausgeführt werden, anstatt zur Verarbeitung an den Dienst gesendet zu werden:

|Direktive                      |Beschreibung|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |Erhalten Sie eine kurze Hilfenachricht.|
|`q`<br>`#quit`<br>`#exit`      |Beenden Sie das Werkzeug.|
|`#a`<br>`#abort`               |Beenden Sie das Werkzeug abrupt.|
|`#clip`                        |Die Ergebnisse der nächsten Abfrage oder des nächsten Befehls werden in die Zwischenablage kopiert.|
|`#cls`                         |Löschen Sie den Konsolenbildschirm.|
|`#connect`*[ConnectionString*]|Stellt eine Verbindung mit einem anderen Kusto-Dienst her (wenn *ConnectionString* weggelassen wird, wird der aktuelle angezeigt.)|
|`#crp`[*Name* Name`=` [ *Wert*]]   |Legt den Wert einer Clientanforderungseigenschaft fest (oder zeigt sie einfach an oder zeigt alle Werte an).|
|`#crp` (`-list` | `-doc`) [*Präfix*]|Listet Clientanforderungseigenschaften auf (nach Präfix oder alle).|
|`#dbcontext`[*Datenbankname*]  |Ändert die von Abfragen und Befehlen verwendete "Kontext"-Datenbank in *DatabaseName* (wenn nicht angegeben, wird der aktuelle Kontext angezeigt).|
|`ke`*Text*                    |Sendet den angegebenen Text an einen ausgeführten Kusto.Explorer-Prozess.|
|`#loop`*Count* *Text*         |Führt den Text mehrmals aus.|
|`#qp`[*Name* Name`=` [ *Wert*]]   |Legt den Wert eines Abfrageparamters fest (oder zeigt ihn einfach an oder zeigt alle Werte an). Einzel-/Doppelanführungszeichen von Anfang/Ende werden gekürzt.|
|`#save`*Dateiname*             |Die Ergebnisse der nächsten Abfrage oder des nächsten Befehls werden in der angegebenen CSV-Datei gespeichert.|
|`#script`*Dateiname*           |Führt das angegebene Skript aus.|
|`#scriptml`*Dateiname*         |Führt das angegebene mehrzeilige Skript aus.|

## <a name="line-input-mode-and-block-input-mode"></a>Line-Eingabemodus und Block-Eingabemodus

Standardmäßig wird Kusto.Cli im **Zeileneingabemodus**ausgeführt: Jedes Zeilenumlaufzeichen wird als Trennzeichen zwischen Abfragen/Befehlen interpretiert, und die Zeile wird sofort zur Ausführung gesendet.

In diesem Modus ist es möglich, eine lange Abfrage oder einen Befehl in mehrere Zeilen aufzuteilen. Das Aussehen `&` des Zeichens als letztes Zeichen einer Zeile (vor der Zeile) bewirkt, dass Kusto.Cli die nächste Zeile weiter liest. Das Erscheinungsbild `&&` des Zeichens als letztes Zeichen einer Zeile (vor der Zeile) bewirkt, dass Kusto.Cli die Newline ignoriert und die nächste Zeile weiterliest.

Alternativ unterstützt Kusto.Cli auch das Ausführen im **Blockeingabemodus:** Durch `-lineMode:false` die Verwendung `#blockmode`des Befehlszeilenschalters oder mit dem Befehl kann man Kusto.Cli anweisen, davon auszugehen, dass jede Zeile eine Fortsetzung der vorherigen Zeile ist, so dass Abfragen und Befehle nur durch eine leere Eingabezeile getrennt werden.

## <a name="comments"></a>Kommentare

Kusto.Cli interpretiert `//` eine Zeichenfolge, die eine neue Zeile als Kommentarzeile beginnt. Es ignoriert den Rest der Zeile und liest die nächste Zeile weiter.

## <a name="tool-only-options"></a>Nur-Werkzeug-Optionen

Befehle                        | Wirkung                                                                            | Derzeit
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | Aktivierungs-/Deaktivieren-Option 'Timing': Anzeige der Zeitanforderungen                    | TRUE
#tableon|#tableoff              | Aktivierungs-/Deaktivieren-Option 'tableView': Format-Ergebnissätze als Tabellen                  | TRUE
#marson|#marsoff                | Aktivierungs-/Deaktivieren-Option 'marsView': Anzeige der 2.bis-letzten Resultsets             | FALSE
#resultson|#resultsoff          | Aktivierungs-/Deaktivieren-Option 'outputResultsSet': Anzeige der Resultsets                 | TRUE
#prettyon|#prettyoff            | Aktivierungs-/Deaktivieren-Option 'prettyErrors': Entfernen Sie unnötigen Goo von Fehlern          | TRUE
#markdownon|#markdownoff        | aktivierungs-/deaktivieren-Option 'markdownView': Tabellen als MarkDown formatieren                   | FALSE
#progressiveon|#progressiveoff  | Aktivierungs-/Deaktivieren-Option 'progressiveView': Progressive Ergebnisse anfordern und anzeigen  | FALSE
#linemode|#blockmode            | Aktivierungs-/Deaktivieren-Option 'lineMode': einzeilige Eingabemodus                          | TRUE

Befehle                              | Wirkung                                                                                                         | Standard
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (ermöglichen|Deaktivieren der Option 'crid': Anzeige der ClientRequestId vor dem Senden der Anforderung)                         | FALSE
#csvheaderson|#csvheadersoff          | (ermöglichen|deaktivieren Option 'csvHeaders': Header in CSV-Ausgabe einschließen)                                            | TRUE
#focuson|#focusoff                    | (ermöglichen|Deaktivieren Sie die Option 'Fokus': Entfernen Sie alle zusätzlichen Flaum und konzentrieren Sie sich auf die richtigen Sachen)                       | FALSE
#linemode|#blockmode                  | (ermöglichen|Deaktivieren Sie die Option 'lineMode': einzeiligen Eingabemodus)                                                     | TRUE
#markdownon|#markdownoff              | (ermöglichen|Deaktivieren der Option 'markdownView': Formattabellen als MarkDown)                                              | FALSE
#marson|#marsoff                      | (ermöglichen|Deaktivieren Sie die Option 'marsView': Anzeige der 2.bis-letzten Resultsets)                                        | FALSE
#prettyon|#prettyoff                  | (ermöglichen|Deaktivieren der Option 'prettyErrors': Entfernen Sie unnötigen Goo von Fehlern)                                     | TRUE
#querystreamingon|#querystreamingoff  | (ermöglichen|Deaktivieren der Option 'queryStreaming': Verwenden Sie den queryStreaming-Endpunkt (nur Kusto-Team))                    | FALSE
#resultson|#resultsoff                | (ermöglichen|Deaktivieren Sie die Option 'outputResultsSet': Anzeige der Resultsets)                                            | TRUE
#tableon|#tableoff                    | (ermöglichen|Deaktivieren der Option 'tableView': Format-Ergebnissätze als Tabellen)                                             | TRUE
#timeon|#timeoff                      | (ermöglichen|Deaktivieren Der Option 'Timing': Anzeige der Zeitanforderungen)                                               | TRUE
#typeon|#typeoff                      | (ermöglichen|Deaktivieren der Option 'typeView': Anzeige des Typs jeder Spalte in der Tabellenansicht (wird Streaming=true erzwingen))  | TRUE
#v2protocolon|#v2protocoloff          | (ermöglichen|deaktivieren Option 'v2protocol': verwenden Sie das v2-Abfrageprotokoll, nicht v1)                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>Verwenden von Kusto.Cli zum Exportieren von Ergebnissen als CSV

Kusto.Cli unterstützt einen speziellen clientseitigen Befehl , `#save`um die **nächsten** Abfrageergebnisse in eine lokale Datei im CSV-Format zu exportieren. Der folgende Aufruf von Kusto.Cli exportiert beispielsweise 10 `StormEvents` Datensätze aus `help.kusto.windows.net` der`Samples` Tabelle im Cluster (Datenbank):

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Verwenden von Kusto.Cli zum Steuern einer laufenden Instanz von Kusto.Explorer

Es ist möglich, Kusto.Cli anzuweisen, mit der "primären" Instanz von Kusto.Explorer zu kommunizieren, die auf dem Computer ausgeführt wird, und es Abfragen zur Ausführung zu senden. Dies kann sehr nützlich für Programme sein, die eine Reihe von Kusto-Abfragen ausführen möchten, aber den Kusto.Explorer-Prozess nicht immer wieder starten möchten. Im folgenden Beispiel wird Kusto.Cli verwendet, um eine Abfrage erneut auszuführen, den Hilfecluster:

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

Die Syntax ist `#ke`sehr einfach: , gefolgt von Leerzeichen und der auszuführenden Abfrage.
Die Abfrage wird dann an die primäre Instanz von Kusto.Explorer gesendet (falls vorhanden) mit dem aktuellen Cluster/Datenbanksatz in Kusto.Cli.