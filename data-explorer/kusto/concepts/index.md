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
ms.localizationpriority: high
ms.openlocfilehash: df4303a56451a3b2723625005a3f38996dc7cf26
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512263"
---
# <a name="getting-started-with-kusto"></a>Erste Schritte mit Kusto

Azure Data Explorer ist ein Dienst zum Speichern und Ausführen interaktiver Analysen für Big Data.

Er basiert auf Managementsystemen für relationale Datenbanken und unterstützt Entitäten wie Datenbanken, Tabellen und Spalten. Komplexe analytische Abfragen werden mithilfe der Kusto-Abfragesprache ausgeführt. Zu den Abfrageoperatoren zählt u. a. Folgendes:
* Berechnete Spalten
* Such- und Filterfunktionen für Zeilen
* Gruppierungsaggregate
* Verknüpfungsfunktionen

Der Dienst bietet eine ausgezeichnete Datenerfassung und Abfrageleistung: 
* Dazu wird auf die Möglichkeit verzichtet, direkte Aktualisierungen einzelner Zeilen und tabellenübergreifende Einschränkungen oder Transaktionen durchzuführen. 
* Herkömmliche Managementsysteme für relationale Datenbanken werden durch den Dienst in Szenarien wie OLTP und Data Warehousing vielmehr ergänzt als ersetzt.
* Der Dienst eignet sich gleichermaßen für die Verarbeitung strukturierter Daten, teilweise strukturierter Daten (etwa JSON-ähnliche geschachtelte Typen) und unstrukturierter Daten (Freitext).

## <a name="interacting-with-azure-data-explorer"></a>Interaktion mit Azure Data Explorer

Für die Interaktion mit Azure Data Explorer (Kusto) verwenden Benutzer in erster Linie Folgendes:
* Verwenden Sie eines der [Abfragetools](../../tools-integrations-overview.md#azure-data-explorer-query-tools). 
* [SQL-Abfragen](../api/tds/t-sql.md)
*  Für die Interaktion wird hauptsächlich die [Kusto-Abfragesprache](../query/index.md) verwendet. KQL ermöglicht das Senden von Datenabfragen und das Verwenden von [Steuerungsbefehlen](../management/index.md) zum Verwalten von Entitäten, Ermitteln von Metadaten und Ähnlichem.
Sowohl Abfragen als auch Steuerungsbefehle sind kurze „Textprogramme“.

## <a name="kusto-queries"></a>Kusto-Abfragen

Eine Abfrage ist eine schreibgeschützte Anforderung, Daten zu verarbeiten und die Ergebnisse dieser Daten zurückzugeben, ohne die Daten oder Metadaten zu ändern. Für Kusto-Abfragen kann die [SQL-Sprache](../api/tds/t-sql.md) oder die [Kusto-Abfragesprache](../query/index.md) verwendet werden. Die folgende Abfrage ist ein Beispiel für Letzteres und ermittelt die Anzahl von Zeilen in der Tabelle `Logs`, in denen der Wert der Spalte `Level` der Zeichenfolge `Critical` entspricht:

```kusto
Logs
| where Level == "Critical"
| count
```

> [!NOTE]
> Abfragen dürfen nicht mit einem Punkt (`.`) oder einer Raute (`#`) beginnen.

## <a name="control-commands"></a>Steuerungsbefehle

Steuerungsbefehle sind Anforderungen an Kusto, um Daten oder Metadaten zu verarbeiten und ggf. auch zu ändern. Der folgende Steuerungsbefehl erstellt beispielsweise eine neue Kusto-Tabelle mit zwei Spalten (`Level` und `Text`):

```kusto
.create table Logs (Level:string, Text:string)
```

Steuerungsbefehle haben eine eigene Syntax. Diese ist nicht Teil der Syntax der Kusto-Abfragesprache, auch wenn es bei zahlreichen Konzepten Überschneidungen gibt. Im Gegensatz zu Abfragen beginnen Steuerungsbefehle mit einem Punkt (`.`).
Diese Unterscheidung verhindert viele Arten von Sicherheitsangriffen, da es somit nicht möglich ist, Steuerungsbefehle einfach in Abfragen einzubetten.

Nicht alle Steuerungsbefehle ändern Daten oder Metadaten. Eine große Klasse von Befehlen, die mit `.show` beginnen, dient zum Anzeigen von Metadaten oder Daten. Der Befehl `.show tables` gibt beispielsweise eine Liste mit allen Tabellen in der aktuellen Datenbank zurück.
