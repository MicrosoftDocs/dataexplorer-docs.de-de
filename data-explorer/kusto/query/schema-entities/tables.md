---
title: 'Tabellen: Azure-Daten-Explorer'
description: In diesem Artikel werden die Tabellen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f51e05abac44b85ab328e7df5645eeab51d2a274
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550621"
---
# <a name="tables"></a>Tabellen

Tabellen sind benannte Entitäten, die Daten enthalten. Eine Tabelle verfügt über einen geordneten Satz von [Spalten](./columns.md)und NULL oder mehr Daten Zeilen. Jede Zeile enthält einen Datenwert für jede Spalte der Tabelle. Die Reihenfolge der Zeilen in der Tabelle ist unbekannt und wirkt sich nicht allgemein auf Abfragen aus, mit Ausnahme einiger tabellarischer Operatoren (z. b. des [Top-Operators](../topoperator.md)), die grundsätzlich nicht bestimmt sind.

Tabellen belegen denselben Namespace wie [gespeicherte Funktionen](./stored-functions.md).
Wenn eine gespeicherte Funktion und eine Tabelle denselben Namen aufweisen, wird die gespeicherte Funktion ausgewählt.

**Hinweise**  

* Bei Tabellennamen wird die Groß-/Kleinschreibung beachtet.
* Tabellennamen entsprechen den Regeln für [Entitäts Namen](./entity-names.md).
* Die maximal zulässige Anzahl von Tabellen pro Datenbank beträgt 10.000.


Ausführliche Informationen zum Erstellen und Verwalten von Tabellen finden Sie unter [Verwalten von Tabellen](../../management/tables.md) .

## <a name="table-references"></a>Tabellen Verweise

Die einfachste Möglichkeit, auf eine Tabelle zu verweisen, ist die Verwendung des Namens. Dieser Verweis kann für alle Tabellen erfolgen, die sich in der-Datenbank im Kontext befinden. Die folgende Abfrage zählt z. b. die Datensätze der Tabelle der aktuellen Datenbank `StormEvents` :

```kusto
StormEvents
| count
```

Eine entsprechende Methode zum Schreiben der obigen Abfrage besteht darin, den Tabellennamen zu beenden:

```kusto
["StormEvents"]
| count
```

Auf Tabellen kann auch verwiesen werden, indem die Datenbank (oder Datenbank und der Cluster) explizit notiert werden. Anschließend können Sie Abfragen erstellen, die Daten aus mehreren Datenbanken und Clustern kombinieren. Beispielsweise kann die folgende Abfrage mit jeder Datenbank im Kontext verwendet werden, solange der Aufrufer Zugriff auf die Zieldatenbank hat:

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

Es ist auch möglich, mithilfe der " [Table ()](../tablefunction.md)"-Funktion auf eine Tabelle zu verweisen, solange das Argument für diese Funktion zu einer Konstanten ausgewertet wird. Beispiel:

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```