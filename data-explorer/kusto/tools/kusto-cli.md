---
title: 'Kusto-CLI: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Kusto-CLI in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ab82698054e4bcb851f9f05acdd62af569e0704b
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258112"
---
# <a name="kusto-cli"></a>Kusto-CLI

Kusto. CLI ist ein Befehlszeilen-Hilfsprogramm, das zum Senden von Anforderungen an Kusto und zum Anzeigen der Ergebnisse verwendet werden kann. Kusto. CLI kann in einem von mehreren Modi ausgeführt werden:

* *Repl-Modus*: der Benutzer gibt Abfragen und Befehle ein, die für Kusto ausgeführt werden, und das Tool zeigt die Ergebnisse an und wartet dann auf den nächsten Benutzer Abfrage/-Befehl.
  ("Repl" steht für "Read/eval/Print/Loop".)

* *Ausführungs Modus*: der Benutzer stellt eine oder mehrere Abfragen und Befehle bereit, die als Befehlszeilenargumente für das Tool ausgeführt werden. Diese werden automatisch nacheinander ausgeführt, und ihre Ergebnisse werden in der Konsole ausgegeben. Wenn Sie alle Eingabe Abfragen und-Befehle ausführen, wechselt das Tool in den repl-Modus.

* *Skript Modus*: vergleichbar mit dem Ausführungs Modus, aber mit den Abfragen und Befehlen, die durch eine Datei (mit dem Namen "Script") angegeben werden.

Kusto. CLI wird hauptsächlich für die Automatisierung von Aufgaben für einen Kusto-Dienst bereitgestellt, der normalerweise erforderlich wäre, um Code zu schreiben (z. b. ein c#-Programm oder ein PowerShell-Skript).

## <a name="getting-the-tool"></a>Holen Sie sich das Tool

Kusto. CLI ist Teil des nuget-Pakets `Microsoft.Azure.Kusto.Tools` , das [hier](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)heruntergeladen werden kann.
Nach dem herunterladen kann der `tools` Ordner des Pakets in den Zielordner extrahiert werden. es ist keine weitere Installation erforderlich (d. h. er ist "xcopy-Installable").



## <a name="running-the-tool"></a>Ausführen des Tools

Kusto. CLI erfordert mindestens ein Befehlszeilenargument, um ausgeführt werden zu können. Normalerweise ist dieses Argument die Verbindungs Zeichenfolge für den Kusto-Dienst, mit dem das Tool eine Verbindung herstellen soll. siehe [Kusto-Verbindungs](../api/connection-strings/kusto.md)Zeichenfolgen. Wenn Sie das Tool ohne Befehlszeilenargumente oder mit einem unbekannten Satz von Argumenten oder mit dem `/help` Schalter ausführen, wird eine Hilfe Meldung an die Konsole ausgegeben.

Verwenden Sie beispielsweise den folgenden Befehl, um "Kusto. CLI" auszuführen und eine Verbindung mit dem `help` Kusto-Dienst herzustellen, und legen Sie den Daten Bank Kontext auf die `Samples` Datenbank fest:

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> Wir haben in der Verbindungs Zeichenfolge Anführungszeichen verwendet, um zu verhindern, dass Shellanwendungen, z. b. PowerShell, das Semikolon ( `;` ) und ähnliche Zeichen in der Verbindungs Zeichenfolge interpretieren.

## <a name="command-line-arguments"></a>Befehlszeilenargumente

`Kusto.Cli.exe`*ConnectionString* [*Switches*]

*ConnectionString*
* Die [Kusto-Verbindungs Zeichenfolge](../api/connection-strings/kusto.md) , die alle Kusto-Verbindungsinformationen enthält.
  Dies ist standardmäßig `net.tcp://localhost/NetDefaultDB`.

`-execute:`*Queryorcommand*
* Wenn angegeben, wird die Befehlszeilenschnittstelle im Ausführungs Modus ausgeführt. die angegebene Abfrage oder der angegebene Befehl wird ausgeführt. Dieser Schalter kann wiederholt werden. in diesem Fall werden die Abfragen/Befehle sequenziell in der Reihenfolge der Darstellung ausgeführt.
  Dieser Switch kann nicht in Verbindung mit `-script` oder verwendet werden `-scriptml` .

`-keepRunning:`*Enablekeeprunning*
* Wenn dies angegeben wird (entweder als `true` oder `false` ), aktiviert oder deaktiviert den Wechsel zum repl-Modus, nachdem alle- `-script` oder- `-execute` Werte verarbeitet wurden.

`-script:`*ScriptFile*
* Wenn angegeben, wird die Befehlszeilenschnittstelle im Skript Modus ausgeführt. die angegebene Skriptdatei wird geladen, und die Abfragen oder Befehle in der Datei werden nacheinander ausgeführt.
  Zeilenumbrüche werden verwendet, um Abfragen/Befehle zu begrenzen, es sei denn, Zeilenenden mit den `&` `&&` Kombinationen von oder, wie unten erläutert.
  Dieser Switch kann nicht in Verbindung mit verwendet werden `-execute` .

`-scriptml:`*ScriptFile*
* Wenn angegeben, wird die Befehlszeilenschnittstelle im Skript Modus ausgeführt. die angegebene Skriptdatei wird geladen, und die Abfragen oder Befehle in der Datei werden nacheinander ausgeführt.
  Die gesamte Skriptdatei wird als einzelne Abfrage oder Befehl behandelt.
  Dieser Switch kann nicht in Verbindung mit verwendet werden `-execute` .

`-echo:`*Enableechomode*
* Wenn dies angegeben wird (entweder als `true` oder `false` ), wird der Echo Modus aktiviert oder deaktiviert.
  Wenn der Echo-Modus aktiviert ist, wird jede Abfrage bzw. jeder Befehl in der Ausgabe wiederholt.

`-transcript:`*"Transcriptfile"*  
* Wenn angegeben, wird die Programmausgabe in *transcriptfile*geschrieben.

`-logToConsole:`*Enablelogdeconsole*
* Wenn angegeben (entweder als `true` oder `false` ), wird das Schreiben der Programmausgabe in die Konsole aktiviert oder deaktiviert.

`-lineMode:`*Enablelinemode*
* Wenn angegeben, wechselt zwischen dem Zeilen Eingabemodus (dem Standardwert oder, wenn auf festgelegt `true` ) und dem Block Eingabemodus (bei Festlegung auf `false` ). Im folgenden finden Sie eine Erläuterung dieser beiden Modi, die bestimmen, wie Zeilenumbrüche behandelt werden.

**Beispiel**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

Bitte beachten Sie, dass kein Leerzeichen zwischen dem Doppelpunkt und dem Argument Wert vorhanden sein sollte.

## <a name="directives"></a>Anweisungen

"Kusto. CLI" unterstützt eine Reihe von Direktiven, die im Tool ausgeführt werden und nicht zur Verarbeitung an den Dienst gesendet werden:

|Anweisung                      |Beschreibung|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |Erhalten Sie eine kurze Hilfe Nachricht.|
|`q`<br>`#quit`<br>`#exit`      |Beenden Sie das Tool.|
|`#a`<br>`#abort`               |Beenden Sie das Tool abgebrochen.|
|`#clip`                        |Die Ergebnisse der nächsten Abfrage oder des nächsten Befehls werden in die Zwischenablage kopiert.|
|`#cls`                         |Löschen Sie den Konsolenbildschirm.|
|`#connect`*[ConnectionString*]|Stellt eine Verbindung mit einem anderen Kusto-Dienst her (wenn " *ConnectionString* " weggelassen wird, wird der aktuelle angezeigt.)|
|`#crp`[*Name* [ `=` *Wert*]]   |Legt den Wert einer Client Anforderungs Eigenschaft fest (oder zeigt ihn einfach an oder zeigt alle Werte an).|
|`#crp`( `-list` \| `-doc` ) [*Präfix*]|Listet Client Anforderungs Eigenschaften (durch Präfix oder alle) auf.|
|`#dbcontext`[*DatabaseName*]  |Ändert die "Kontext"-Datenbank, die von Abfragen und Befehlen in *DatabaseName* verwendet wird (falls nicht angegeben, wird der aktuelle Kontext angezeigt.)|
|`ke` *Text*                    |Sendet den angegebenen Text an einen laufenden Kusto. Explorer-Prozess.|
|`#loop`*Count* *Text* zählen         |Führt den Text mehrmals aus.|
|`#qp`[*Name* [ `=` *Wert*]]   |Legt den Wert eines Abfrage paramters fest (oder zeigt ihn einfach an oder zeigt alle Werte an). Einfache und doppelte Anführungszeichen am Anfang/Ende werden gekürzt.|
|`#save`*Dateiname*             |Die Ergebnisse der nächsten Abfrage oder des nächsten Befehls werden in der angegeben CSV-Datei gespeichert.|
|`#script`*Dateiname*           |Führt das gekennzeichnete Skript aus.|
|`#scriptml`*Dateiname*         |Führt das gekennzeichnete mehrzeilige Skript aus.|

## <a name="line-input-mode-and-block-input-mode"></a>Zeilen Eingabemodus und Block Eingabemodus

Standardmäßig wird "Kusto. CLI" im **Zeilen Eingabemodus**ausgeführt: jedes neue Zeilen Trennzeichen wird als Trennzeichen zwischen Abfragen/Befehlen interpretiert, und die Zeile wird sofort zur Ausführung gesendet.

In diesem Modus ist es möglich, eine lange Abfrage oder einen Befehl in mehrere Zeilen zu unterbrechen. Die Darstellung des `&` Zeichens als letztes Zeichen einer Zeile (vor dem Zeilen Vorstrich) bewirkt, dass Kusto. CLI das Lesen der nächsten Zeile fortsetzt. Die Darstellung des `&&` Zeichens als letztes Zeichen einer Zeile (vor dem Zeilen Vorstrich) bewirkt, dass Kusto. CLI den Zeilen Vorstrich ignoriert und das Lesen der nächsten Zeile fortsetzt.

Alternativ unterstützt Kusto. CLI auch das Ausführen im **Block Eingabemodus**: entweder mithilfe des Befehls Zeilenschalters `-lineMode:false` oder mithilfe des Befehls `#blockmode` , kann Kusto. CLI davon ausgehen, dass jede Zeile eine Fortsetzung der vorherigen Zeile ist, sodass Abfragen und Befehle nur durch eine leere Eingabezeile getrennt werden.

## <a name="comments"></a>Kommentare

Kusto. CLI interpretiert eine `//` Zeichenfolge, die eine neue Zeile startet, als Kommentarzeile. Der Rest der Zeile wird ignoriert, und das Lesen der nächsten Zeile wird fortgesetzt.

## <a name="tool-only-options"></a>Tool-only-Optionen

Befehle                        | Wirkung                                                                            | Aktuell
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | Option "Timing" aktivieren/deaktivieren: zeigt die benötigte Zeit an                    | TRUE
#tableon|#tableoff              | Aktivieren/Deaktivieren der Option "tableView": Formatieren von Resultsets als Tabellen                  | TRUE
#marson|#marsoff                | Option "Mars View" aktivieren/deaktivieren: zeigt die 2. bis letzten Resultsets an             | FALSE
#resultson|#resultsoff          | Option "outputresultset" aktivieren/deaktivieren: zeigt die Resultsets an                 | TRUE
#prettyon|#prettyoff            | Option "prettyerrors" aktivieren/deaktivieren: unnötige Goo aus Fehlern entfernen          | TRUE
#markdownon|#markdownoff        | Aktivieren/Deaktivieren der Option "markdownview": Formatieren von Tabellen als markdown                   | FALSE
#progressiveon|#progressiveoff  | Aktivieren/Deaktivieren der Option ' progressiveview ': anfordern und anzeigen progressiver Ergebnisse  | FALSE
#linemode|#blockmode            | Aktivieren/Deaktivieren der Option "Linemode": einzeiligen Eingabemodus                          | TRUE

Befehle                              | Wirkung                                                                                                         | Standard
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (aktivieren|Option "CRID" deaktivieren: zeigt die clientrequestid vor dem Senden der Anforderung an.)                         | FALSE
#csvheaderson|#csvheadersoff          | (aktivieren|Option "csvheaders" deaktivieren: Header in CSV-Ausgabe einschließen)                                            | TRUE
#focuson|#focusoff                    | (aktivieren|Option ' Fokus ' deaktivieren: Entfernen Sie alle zusätzlichen zielgerichtete, und konzentrieren Sie sich auf die richtigen Dinge.)                       | FALSE
#linemode|#blockmode                  | (aktivieren|Option ' Linemode ' deaktivieren: einzeiligen Eingabemodus)                                                     | TRUE
#markdownon|#markdownoff              | (aktivieren|Deaktivieren der Option "markdownview": Formatieren von Tabellen als markdown)                                              | FALSE
#marson|#marsoff                      | (aktivieren|Option ' Mars View ' deaktivieren: zeigt die 2. bis letzten Resultsets an                                        | FALSE
#prettyon|#prettyoff                  | (aktivieren|Option "prettyerrors" deaktivieren: unnötige Goo aus Fehlern entfernen)                                     | TRUE
#querystreamingon|#querystreamingoff  | (aktivieren|Option ' querystreaming ' deaktivieren: Verwenden Sie den querystreaming-Endpunkt (nur Kusto-Team)).                    | FALSE
#resultson|#resultsoff                | (aktivieren|Option "outputresultset" deaktivieren: zeigt die Resultsets an)                                            | TRUE
#tableon|#tableoff                    | (aktivieren|Option "tableView" deaktivieren: Ergebnissätze als Tabellen formatieren)                                             | TRUE
#timeon|#timeoff                      | (aktivieren|Option "Timing" deaktivieren: zeigt die benötigte Zeit an.                                               | TRUE
#typeon|#typeoff                      | (aktivieren|Option "typeview" deaktivieren: zeigt den Typ der einzelnen Spalten in der Tabellenansicht an (zwingt Streaming = true))  | TRUE
#v2protocolon|#v2protocoloff          | (aktivieren|Option ' v2protocol ' deaktivieren: Verwenden Sie das v2-Abfrageprotokoll, nicht v1).                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>Verwenden von Kusto. CLI zum Exportieren von Ergebnissen als CSV

Kusto. CLI unterstützt einen speziellen Client seitigen Befehl, `#save` , um die **nächsten** Abfrageergebnisse in eine lokale Datei im CSV-Format zu exportieren. Beispielsweise exportiert der folgende Aufruf von Kusto. CLI 10 Datensätze aus der `StormEvents` Tabelle im `help.kusto.windows.net` Cluster (- `Samples` Datenbank):

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Verwenden von Kusto. CLI zum Steuern einer laufenden Instanz von Kusto. Explorer

Es ist möglich, Kusto. CLI anzuweisen, mit der "primären" Instanz von Kusto. Explorer zu kommunizieren, die auf dem Computer ausgeführt wird, und IT-Abfragen zur Ausführung zu senden. Dies kann für Programme sehr nützlich sein, die eine Reihe von Kusto-Abfragen ausführen möchten, aber den Kusto. Explorer-Prozess nicht erneut starten möchten. Im folgenden Beispiel wird Kusto. CLI verwendet, um eine Abfrage für den Hilfe Cluster auszuführen:

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

Die Syntax ist sehr einfach: `#ke` , gefolgt von Leerzeichen und der Abfrage, die ausgeführt werden soll.
Die Abfrage wird dann an die primäre Instanz von Kusto. Explorer (sofern vorhanden) mit dem aktuellen Cluster/Daten Bank Satz in Kusto. CLI gesendet.
