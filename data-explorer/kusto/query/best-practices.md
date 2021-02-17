---
title: 'Bewährte Methoden für Abfragen: Azure Data Explorer'
description: In diesem Artikel werden bewährte Methode für Abfragen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 87154368a033afe1da7669e71e269081865b689d
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359912"
---
# <a name="query-best-practices"></a>Best Practices für Abfragen

Es stehen einige bewährte Methoden zur Verfügung, damit die Abfrage schneller ausgeführt wird.

|Aktion  |Verwenden  |Nicht verwenden  |Notizen  |
|---------|---------|---------|---------|
| **Zeitfilter** | Verwenden Sie zunächst Zeitfilter. ||Kusto ist für die Verwendung von Zeitfiltern hochgradig optimiert.| 
|**Zeichenfolgenoperatoren**      | Verwenden Sie den `has`-Operator     | Verwenden Sie nicht `contains`.     | Bei der Suche nach vollständigen Token funktioniert `has` besser, da keine Teilzeichenfolgen gesucht werden.   |
|**Operatoren, die zwischen Groß- und Kleinschreibung unterscheiden**     |  Verwenden Sie `==`       | Verwenden Sie nicht `=~`.       |  Verwenden Sie nach Möglichkeit Operatoren, die zwischen Groß- und Kleinschreibung unterscheiden.       |
| | Verwenden Sie `in` | Verwenden Sie nicht `in~`.|
|  | Verwenden Sie `contains_cs`         | Verwenden Sie nicht `contains`.        | Wenn Sie `has`/`has_cs` verwenden können und `contains`/`contains_cs` nicht verwenden, ist dies noch besser. |
| **Durchsuchen von Text**    |    Suchen in einer bestimmten Spalte     |    Verwenden Sie nicht `*`.    |   `*` führt eine Volltextsuche über alle Spalten hinweg durch.    |
| **Extrahieren von Feldern aus [dynamischen Objekten](./scalar-data-types/dynamic.md) in Millionen von Zeilen**    |  Materialisieren Sie die Spalte zur Erfassungszeit, wenn die meisten Ihrer Abfragen Felder aus dynamischen Objekten aus Millionen von Zeilen extrahieren.      |         | Auf diese Weise fällt die Spaltenextraktion nur ein Mal an.    |
| **Lookup für seltene Schlüssel/Werte in [dynamischen Objekten](./scalar-data-types/dynamic.md)**    |  Verwenden Sie `MyTable | where DynamicColumn has "Rare value" | where DynamicColumn.SomeKey == "Rare value"` | Verwenden Sie nicht `MyTable | where DynamicColumn.SomeKey == "Rare value"`. | Auf diese Weise werden die meisten Datensätze herausgefiltert, und die JSON-Analyse wird nur für den Rest ausgeführt. |
| **`let`-Anweisung mit einem Wert, den Sie mehrmals verwenden** | Verwenden Sie die [materialize()-Funktion](./materializefunction.md). |  |   Weitere Informationen zur Verwendung von `materialize()` finden Sie unter [materialize()](materializefunction.md).|
| **Anwenden von Konvertierungen auf mehr als 1 Milliarde Datensätze**| Strukturieren Sie die Abfrage neu, um die Datenmenge zu reduzieren, die in die Konvertierung eingespeist wird.| Konvertieren Sie keine großen Datenmengen, wenn dies vermieden werden kann. | |
| **Neue Abfragen** | Verwenden Sie `limit [small number]` oder `count` am Ende. | |     Das Ausführen von ungebundenen Abfragen für unbekannte Datasets kann zu Ergebnissen im GB-Bereich führen, die an den Client zurückgegeben werden, was zu einer langsamen Reaktion und hoher Auslastung des Clusters führt.|
| **Vergleiche ohne Unterscheidung von Groß-/Kleinschreibung** | Verwenden Sie `Col =~ "lowercasestring"` | Verwenden Sie nicht `tolower(Col) == "lowercasestring"`. |
| **Vergleichen von Daten in Kleinbuchstaben (oder Großbuchstaben)** | `Col == "lowercasestring"` (oder `Col == "UPPERCASESTRING"`) | Vermeiden Sie die Verwendung von Vergleichen,die keine Groß-/Kleinschreibung berücksichtigen.||
| **Filtern nach Spalten** |  Filtern Sie nach einer Tabellenspalte.|Filtern Sie nicht nach einer berechneten Spalte. | |
| | Verwenden Sie `T | where predicate(<expression>)` | Verwenden Sie nicht `T | extend _value = <expression> | where predicate(_value)`. ||
| **summarize-Operator** |  Verwenden Sie [hint.strategy=shuffle](./shufflequery.md), wenn `group by keys` des summarize-Operators eine hohe Kardinalität aufweist. | | Hohe Kardinalität liegt idealerweise über 1 Million.|
|**[join-Operator](./joinoperator.md)** | Wählen Sie die Tabelle mit weniger Zeilen aus, die die erste Tabelle sein soll (in der Abfrage ganz links). ||
| Join über Cluster hinweg |Führen Sie die Abfrage über Cluster hinweg auf der „rechten“ Seite des Joins aus, auf der sich die meisten Daten befinden. ||
|Join, wenn die linke Seite klein und die rechte Seite groß ist | Verwenden Sie [hint.strategy=broadcast](./broadcastjoin.md). || „Klein“ bezieht sich auf bis zu 100.000 Datensätze. |
|Join, wenn beide Seiten zu groß sind | Verwenden Sie [hint.strategy=shuffle](./shufflequery.md). || Verwenden Sie diese Option, wenn der Joinschlüssel über hohe Kardinalität verfügt.|
|**Extrahieren von Werten in einer Spalte mit Zeichenfolgen, die dasselbe Format oder Muster aufweisen**|  Verwenden Sie den [parse-Operator](./parseoperator.md). | Verwenden Sie nicht mehrere `extract()`-Anweisungen.  | Beispielwerte Werte wie `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`.
|**[extract()-Funktion](./extractfunction.md)**| Verwenden Sie diese Option, wenn nicht alle analysierten Zeichenfolgen dem gleichen Format oder Muster folgen.| |Extrahieren Sie die erforderlichen Werte mithilfe von REGEX.|
| **[materialize()-Funktion](./materializefunction.md)** | Pushen Sie alle möglichen Operatoren, die das materialisierte Dataset verkleinern und trotzdem die Semantik der Abfrage beibehalten. | |Beispielsweise Filter oder projekteigene erforderliche Spalten.

