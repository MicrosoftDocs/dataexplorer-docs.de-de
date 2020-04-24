---
title: Tabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Tabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fd47a8f59c716148dfc5f89ac1d4c7aca45a8e9c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509281"
---
# <a name="tables"></a>Tabellen

Tabellen sind benannte Entitäten, die Daten enthalten. Eine Tabelle enthält einen geordneten Satz von [Spalten](./columns.md)und null oder mehr Datenzeilen, wobei jede Zeile einen Datenwert für jede der Spalten der Tabelle enthält. Die Reihenfolge der Zeilen in der Tabelle ist unbekannt und wirkt sich im Allgemeinen nicht auf Abfragen aus, mit Ausnahme einiger tabellarischer Operatoren (z. B. des [top-Operators](../topoperator.md)), die von Natur aus unbestimmt sind.

Tabellen belegen denselben Namespace wie [gespeicherte Funktionen,](./stored-functions.md)wenn also eine gespeicherte Funktion und eine Tabelle denselben Namen haben, wird die gespeicherte Funktion ausgewählt.

**Hinweise**  

* Bei Den Tabellennamen wird die Groß-/Kleinschreibung beachtet.
* Tabellennamen folgen den Regeln für [Entitätsnamen](./entity-names.md).
* Die maximale Anzahl von Tabellen pro Datenbank beträgt 10.000.


Details zum Erstellen und Verwalten von Tabellen finden Sie unter Verwalten von [Tabellen](../../management/tables.md)

## <a name="table-references"></a>Tabellenreferenzen

Die einfachste Möglichkeit, auf eine Tabelle zu verweisen, besteht darin, ihren Namen zu verwenden. Dies kann für alle Tabellen erfolgen, die sich im Kontext in der Datenbank befinden. So zählt z. B. die folgende Abfrage die `StormEvents` Datensätze der aktuellen Datenbanktabelle:

```kusto
StormEvents
| count
```

Beachten Sie, dass eine gleichwertige Möglichkeit zum Schreiben der obigen Abfrage darin besteht, den Tabellennamen zu entkommen:

```kusto
["StormEvents"]
| count
```

Tabellen können auch referenziert werden, indem explizit die Datenbank (oder Datenbank und Cluster) notiert wird, in der sie sich befinden, sodass Abfragen erstellt werden können, die Daten aus mehreren Datenbanken und Clustern kombinieren. Die folgende Abfrage funktioniert z. B. mit jeder Datenbank im Kontext, solange der Aufrufer Zugriff auf die Zieldatenbank hat:

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

Es ist auch möglich, auf eine Tabelle zu verweisen, indem Sie die [spezielle Funktion table()](../tablefunction.md)verwenden, solange das Argument für diese Funktion zu einer Konstante ausgewertet wird. Beispiel:

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```