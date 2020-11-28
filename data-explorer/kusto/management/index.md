---
title: Übersicht über die Verwaltung (Steuerungsbefehle) – Azure Data Explorer | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über die Verwaltung (Steuerungsbefehle) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7a0a2384409f4b1c7e55231d8bf7b68c44ab8a6d
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303278"
---
# <a name="management-control-commands-overview"></a>Übersicht über die Verwaltung (Steuerungsbefehle)

In diesem Abschnitt werden die Steuerungsbefehle für die Verwaltung von Kusto beschrieben.
Steuerungsbefehle sind Anforderungen, die an den Dienst gesendet werden, um Informationen abzurufen, bei denen es sich nicht notwendigerweise um Daten aus den Datenbanktabellen handelt, oder um beispielsweise den Zustand des Diensts zu ändern.

## <a name="differentiating-control-commands-from-queries"></a>Unterscheiden zwischen Steuerungsbefehlen und Abfragen

Abfragen und Steuerungsbefehle werden von Kusto auf drei Ebenen unterschieden: auf der Sprachebene, auf der Protokollebene und auf der API-Ebene. Diese Unterscheidung erfolgt aus Sicherheitsgründen.

Auf der Sprachebene entscheidet das erste Zeichen des Anforderungstexts, ob es sich bei der Anforderung um einen Steuerungsbefehl oder um eine Abfrage handelt. Steuerungsbefehle müssen mit einem Punkt (`.`) beginnen; Abfragen dürfen dagegen nicht mit diesem Zeichen beginnen.

Auf der Protokollebene werden für Steuerungsbefehle und Abfragen jeweils unterschiedliche HTTP/HTTPS-Endpunkte verwendet.

Auf der API-Ebene werden zum Senden von Steuerungsbefehlen und Abfragen jeweils unterschiedliche Funktionen verwendet.

## <a name="combining-queries-and-control-commands"></a>Kombinieren von Abfragen und Steuerungsbefehlen

Steuerungsbefehle können auf andere Steuerungsbefehle sowie auf Abfragen verweisen (aber nicht umgekehrt).
Dabei werden verschiedene Szenarien unterstützt:

1. **AdminThenQuery:** Ein Steuerungsbefehl wird ausgeführt, und das Ergebnis (dargestellt als temporäre Datentabelle) wird als Eingabe für eine Abfrage verwendet.
2. **AdminFromQuery:** Eine Abfrage oder ein Administratorbefehl vom Typ `.show` wird ausgeführt, und das Ergebnis (dargestellt als temporäre Datentabelle) wird als Eingabe für einen Steuerungsbefehl verwendet.

Da die gesamte Kombination ein Steuerungsbefehl und keine Abfrage ist, muss der Text der Anforderung mit einem Punkt (`.`) beginnen, und die Anforderung muss an den Verwaltungsendpunkt des Diensts gesendet werden.

Beachten Sie auch, dass [Abfrageanweisungen](../query/statements.md) innerhalb des Abfrageteils des Texts angegeben werden. (Sie können nicht vor dem eigentlichen Befehl platziert werden.)

>[!NOTE]
> Führen Sie Vorgänge vom Typ „[command-then-query]“ nicht zu häufig aus.
> *command-then-query* leitet das Resultset des Steuerungsbefehl weiter und wendet Filter/Aggregationen darauf an.
>  * Beispiel: `.show ... | where ... | summarize ...`
>   * Bei der Ausführung von Befehlen wie: `.show cluster extents | count` (Schwerpunkt auf `| count`) bereitet Kusto zuerst eine Datentabelle vor, die alle Details aller Erweiterungen im Cluster enthält. Das System sendet dann diese speicherinterne Tabelle an die Kusto-Engine, um die Anzahl zu ermitteln. Das System arbeitet tatsächlich hart in einem nicht optimierten Pfad, um Ihnen eine solche einfache Antwort zu geben.


**AdminThenQuery** kann auf zwei Arten angegeben werden:

1. Durch Verwendung eines senkrechten Strichs (`|`). In diesem Fall werden die Ergebnisse des Steuerungsbefehls wie ein gewöhnlicher Abfrageoperator behandelt, der Daten generiert.
2. Durch Verwendung eines Semikolons (`;`). In diesem Fall werden die Ergebnisse des Steuerungsbefehls in ein spezielles Symbol namens `$command_results` eingefügt, das dann beliebig oft in der Abfrage verwendet werden kann.

Beispiel:

```kusto
// 1. Using pipe: Count how many tables are in the database-in-scope:
.show tables
| count

// 2. Using semicolon: Count how many tables are in the database-in-scope:
.show tables;
$command_results
| count

// 3. Using semicolon, and including a let statement:
.show tables;
let useless=(n:string){strcat(n,'-','useless')};
$command_results | extend LastColumn=useless(TableName)
```

**AdminFromQuery** wird durch die Zeichenkombination `<|` angegeben. Im folgenden Beispiel wird zuerst eine Abfrage ausgeführt, die eine Tabelle mit einer einzelnen Spalte (namens `str` vom Typ `string`) und einer einzelnen Zeile generiert. Diese wird dann als Tabellenname `MyTable` in die Datenbank im Kontext geschrieben:

```kusto
.set MyTable <|
let text="Hello, World!";
print str=text
```


