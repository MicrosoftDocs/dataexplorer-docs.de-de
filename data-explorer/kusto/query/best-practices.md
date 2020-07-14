---
title: 'Bewährte Methoden für Abfragen: Azure Daten-Explorer'
description: In diesem Artikel werden bewährte Methoden für Abfragen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 301917f363176fb8e3bbf2fe4286a86b7a5674ea
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280524"
---
# <a name="query-best-practices"></a>Best Practices für Abfragen

Es folgen einige bewährte Methoden, die Sie befolgen sollten, um die Abfrage schneller auszuführen.

|Aktion  |Verwendung  |Nicht verwenden  |Notizen  |
|---------|---------|---------|---------|
| **Zeitfilter** | Verwenden Sie zunächst Zeitfilter. ||Kusto ist für die Verwendung von Zeit Filtern stark optimiert.| 
|**Zeichenfolgenoperatoren**      | Verwenden des `has` Operators     | Nicht verwenden`contains`     | Wenn Sie nach vollständigen Token suchen, `has` funktioniert besser, da keine Teil Zeichenfolgen gesucht werden.   |
|**Operatoren mit Berücksichtigung der groß-**     |  Verwenden von `==`       | Nicht verwenden`=~`       |  Verwenden Sie nach Möglichkeit die Operatoren mit Berücksichtigung der groß-       |
| | Verwenden von `in` | Nicht verwenden`in~`|
|  | Verwenden von `contains_cs`         | Nicht verwenden`contains`        | Wenn Sie verwenden `has` / `has_cs` und nicht verwenden können `contains` / `contains_cs` , ist das noch besser. |
| **Suchen von Text**    |    In einer bestimmten Spalte suchen     |    Nicht verwenden`*`    |   `*`führt eine Volltextsuche über alle Spalten hinweg durch.    |
| **Felder aus [dynamischen Objekten](./scalar-data-types/dynamic.md) in Millionen von Zeilen extrahieren**    |  Materialisieren Sie die Spalte bei der Erfassungs Zeit, wenn die meisten Ihrer Abfragen Felder aus dynamischen Objekten in Millionen von Zeilen extrahieren.      |         | Auf diese Weise zahlen Sie nur einmal für die Spalten Extraktion.    |
| **`let`Anweisung mit einem Wert, den Sie mehrmals verwenden** | Verwenden der [Materialize ()-Funktion](./materializefunction.md) |  |   Weitere Informationen zur Verwendung von finden Sie unter `materialize()` [Materialize ()](materializefunction.md).|
| **Anwenden von Konvertierungen auf mehr als 1 Milliarde Datensätze**| Strukturieren Sie die Abfrage neu, um die Datenmenge zu reduzieren, die in die Konvertierung eingespeist wird.| Konvertieren Sie große Datenmengen nicht, wenn Sie vermieden werden können. | |
| **Neue Abfragen** | Verwenden Sie `limit [small number]` oder `count` am Ende. | |     Das Ausführen von ungebundenen Abfragen über unbekannte Datasets kann GSB der Ergebnisse ergeben, die an den Client zurückgegeben werden. Dies führt zu einer langsamen Antwort und einem ausgelasteten Cluster.|
| **Vergleichs Vergleiche** | Verwenden von `Col =~ "lowercasestring"` | Nicht verwenden`tolower(Col) == "lowercasestring"` |
| **Vergleichen von Daten in Kleinbuchstaben (oder in Großbuchstaben)** | `Col == "lowercasestring"` (oder `Col == "UPPERCASESTRING"`) | Vermeiden Sie die Verwendung von vergleichen ohne Beachtung||
| **Filtern nach Spalten** |  Filtern Sie nach einer Tabellenspalte.|Filtern Sie nicht nach einer berechneten Spalte. | |
| | Verwenden von `T | where predicate(<expression>)` | Nicht verwenden`T | extend _value = <expression> | where predicate(_value)` ||
| **summarize-Operator** |  Verwenden Sie [Hint. Strategy = shuffle](./shufflequery.md) , wenn der des Zusammenfassungs `group by keys` Operators eine hohe Kardinalität hat. | | Hohe Kardinalität liegt idealerweise über 1 Million.|
|**[join-Operator](./joinoperator.md)** | Wählen Sie die Tabelle mit den weniger Zeilen aus, die die erste Tabelle sein soll (in der Abfrage ganz links). ||
| Cluster übergreifende Einbindung |Führen Sie die Abfrage Cluster übergreifend auf der rechten Seite des Joins aus, auf der sich die meisten Daten befinden. ||
|Join, wenn die linke Seite klein und die Rechte Seite groß ist | Use [Hint. Strategy = Broadcast](./broadcastjoin.md) || Small bezieht sich auf bis zu 100.000 Datensätze. |
|Beitreten, wenn beide Seiten zu groß sind | Use [Hint. Strategy = shuffle](./shufflequery.md) || Verwenden Sie, wenn der joinschlüssel über eine hohe Kardinalität verfügt.|
|**Extrahieren von Werten in einer Spalte mit Zeichen folgen, die dasselbe Format oder Muster aufweisen**|  Verwenden des Analyse [Operators](./parseoperator.md) | Verwenden Sie nicht mehrere- `extract()` Anweisungen.  | Beispielsweise werden Werte wie`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`
|**[Extract ()-Funktion](./extractfunction.md)**| Verwenden Sie bei analysierten Zeichen folgen nicht alle das gleiche Format oder Muster.| |Extrahieren Sie die erforderlichen Werte mithilfe eines Regex.|
| **[Materialize ()-Funktion](./materializefunction.md)** | Übermitteln Sie alle möglichen Operatoren, die das materialisierte DataSet verringern, und behalten Sie trotzdem die Semantik der Abfrage bei. | |Beispielsweise Filter oder nur erforderliche Spalten.
