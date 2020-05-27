---
title: Erste Schritte mit Kusto
description: In diesem Artikel werden die ersten Schritte mit Kusto beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a56878c8b79e651afcd1b8ce18a3220dc304211
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863386"
---
# <a name="getting-started-with-kusto"></a>Erste Schritte mit Kusto

Azure Data Explorer ist ein Dienst zum Speichern und Ausführen interaktiver Analysen für Big Data.

Er basiert auf Managementsystemen für relationale Datenbanken und unterstützt Entitäten wie Datenbanken, Tabellen und Spalten. Komplexe analytische Abfragen werden mithilfe der Kusto-Abfragesprache ausgeführt. Zu den Abfrageoperatoren zählen u. a. berechnete Spalten, Such- und Filterfunktionen für Zeilen sowie Gruppierungsaggregate und Joins.

Der Dienst bietet eine erstklassige Datenerfassungs- und Abfrageleistung. Dazu wird auf die Möglichkeit verzichtet, direkte Aktualisierungen einzelner Zeilen oder tabellenübergreifende Einschränkungen/Transaktionen durchzuführen. Herkömmliche Managementsysteme für relationale Datenbanken werden dadurch in Szenarien wie OLTP und Data Warehousing vielmehr ergänzt als ersetzt.

Der Dienst eignet sich gleichermaßen für die Verarbeitung strukturierter Daten, teilweise strukturierter Daten (etwa JSON-ähnliche geschachtelte Typen) und unstrukturierter Daten (Freitext).

## <a name="interacting-with-azure-data-explorer"></a>Interaktion mit Azure Data Explorer

Benutzer interagieren mit Kusto in erster Linie über eines der zahlreichen verfügbaren [Clienttools](../tools/index.md). [SQL-Abfragen](../api/tds/t-sql.md) werden zwar unterstützt, für die Interaktion werden jedoch hauptsächlich die [Kusto-Abfragesprache](../query/index.md) (zum Senden von Datenabfragen) und [Steuerungsbefehle](../management/index.md) (zum Verwalten von Entitäten, Ermitteln von Metadaten und Ähnlichem) verwendet. Sowohl Abfragen als auch Steuerungsbefehle sind im Grunde kurze „Textprogramme“.

## <a name="kusto-queries"></a>Kusto-Abfragen

Eine Abfrage ist eine schreibgeschützte Anforderung, Daten zu verarbeiten und die Ergebnisse dieser Daten zurückzugeben, ohne die Daten oder Metadaten zu ändern. Für Kusto-Abfragen kann die [SQL-Sprache](../api/tds/t-sql.md) oder die [Kusto-Abfragesprache](../query/index.md) verwendet werden. Die folgende Abfrage ist ein Beispiel für Letzteres und ermittelt die Anzahl von Zeilen in der Tabelle `Logs`, in denen der Wert der Spalte `Level` der Zeichenfolge `Critical` entspricht:

```kusto
Logs
| where Level == "Critical"
| count
```

Abfragen dürfen nicht mit einem Punkt (`.`) oder einer Raute (`#`) beginnen.

## <a name="control-commands"></a>Steuerungsbefehle

Steuerungsbefehle sind Anforderungen an Kusto, um Daten oder Metadaten zu verarbeiten und ggf. auch zu ändern. Der folgende Steuerungsbefehl erstellt beispielsweise eine neue Kusto-Tabelle mit zwei Spalten (`Level` und `Text`):

```kusto
.create table Logs (Level:string, Text:string)
```

Steuerungsbefehle haben eine eigene Syntax. (Diese ist nicht Teil der Syntax der Kusto-Abfragesprache, auch wenn es bei zahlreichen Konzepten Überschneidungen gibt.) Im Gegensatz zu Abfragen beginnen Steuerungsbefehle mit einem Punkt (`.`).
Diese Unterscheidung verhindert viele Arten von Sicherheitsangriffen, da es somit nicht möglich ist, Steuerungsbefehle einfach in Abfragen einzubetten.

Nicht alle Steuerungsbefehle ändern Daten oder Metadaten. Eine große Klasse von Befehlen (Befehle, die mit `.show` beginnen) dient zum Anzeigen von Metadaten oder Daten. Der Befehl `.show tables` gibt beispielsweise eine Liste mit allen Tabellen in der aktuellen Datenbank zurück.
