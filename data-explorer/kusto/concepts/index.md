---
title: Erste Schritte mit Kusto – Azure Data Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden die ersten Schritte mit Kusto in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b66dd59dd17f1671c68e63e35ee62f56e6ec8fe
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610332"
---
# <a name="getting-started-with-kusto"></a>Erste Schritte mit Kusto

Kusto ist ein Dienst zum Speichern und Ausführen interaktiver Analysen für Big Data.

Er basiert auf relationalen Managementsystemen für relationale Datenbanken, unterstützt Entitäten wie Datenbanken, Tabellen und Spalten und bietet Abfrageoperatoren für komplexe Analysen. Hierzu zählen beispielsweise berechnete Spalten, Such- und Filterfunktionen für Zeilen sowie Gruppierungsaggregate und Joins.

Kusto bietet eine erstklassige Datenerfassungs- und Abfrageleistung. Dazu wird auf die Möglichkeit verzichtet, direkte Aktualisierungen einzelner Zeilen oder tabellenübergreifende Einschränkungen/Transaktionen durchzuführen. Herkömmliche Managementsysteme für relationale Datenbanken werden dadurch in Szenarien wie OLTP und Data Warehousing vielmehr ergänzt als ersetzt.

Als Big Data-Dienst eignet sich Kusto gleichermaßen für die Verarbeitung strukturierter Daten, teilweise strukturierter Daten (etwa JSON-ähnliche geschachtelte Typen) und unstrukturierter Daten (Freitext).

## <a name="interacting-with-kusto"></a>Interagieren mit Kusto

Benutzer interagieren mit Kusto in erster Linie über eines der zahlreichen [Clienttools](../tools/index.md), die für Kusto zur Verfügung stehen. [SQL-Abfragen](../api/tds/t-sql.md) werden für Kusto zwar unterstützt, für die Interaktion mit Kusto werden jedoch hauptsächlich die [Kusto-Abfragesprache](../query/index.md) (zum Senden von Datenabfragen) und [Steuerungsbefehle](../management/index.md) (zum Verwalten von Kusto-Entitäten, Ermitteln von Metadaten und Ähnlichem) verwendet. Sowohl Abfragen als auch Steuerungsbefehle sind im Grunde kurze „Textprogramme“.

## <a name="queries"></a>Abfragen

Eine Kusto-Abfrage ist eine schreibgeschützte Anforderung, um Kusto-Daten zu verarbeiten und die Ergebnisse der Verarbeitung zurückzugeben, ohne die Kusto-Daten oder die Metadaten zu ändern. Für Kusto-Abfragen kann die [SQL-Sprache](../api/tds/t-sql.md) oder die [Kusto-Abfragesprache](../query/index.md) verwendet werden.
Die folgende Abfrage ist ein Beispiel für Letzteres und ermittelt die Anzahl von Zeilen in der Tabelle `Logs`, in denen der Wert der Spalte `Level` der Zeichenfolge `Critical` entspricht:

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

Nicht alle Steuerungsbefehle ändern Kusto-Daten oder Metadaten. Eine große Klasse von Befehlen (Befehle, die mit `.show` beginnen) dient zum Anzeigen von Metadaten oder Daten zu Kusto. Der Befehl `.show tables` gibt beispielsweise eine Liste mit allen Tabellen in der aktuellen Datenbank zurück.
