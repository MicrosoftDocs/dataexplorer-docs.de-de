---
title: Kusto-MS-TDS/T-SQL-Unterschiede mit SQL Server-Azure Daten-Explorer
description: In diesem Artikel werden MS-TDS/T-SQL-Unterschiede zwischen Kusto-Microsoft SQL Server in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: c949b5bb3659d82ed586a39b4310495e61934777
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382368"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>MS-TDS/T-SQL-Unterschiede zwischen Kusto-Microsoft SQL Server

Im folgenden finden Sie eine partielle Liste der Hauptunterschiede zwischen Kusto und SQL Server Implementierung von T-SQL.

## <a name="create-insert-drop-alter-statements"></a>Create-, INSERT-, Drop-, Alter-Anweisungen

Kusto unterstützt keine Schema Änderungen oder Datenänderungen durch MS-TDS und unterstützt auch die oben genannten t-SQL-Anweisungen nicht.

## <a name="correlated-sub-queries"></a>Korrelierte Unterabfragen

Kusto unterstützt keine korrelierten Unterabfragen in den `SELECT` `WHERE` Klauseln, und `JOIN` .

## <a name="top-flavors"></a>Top-Varianten

Kusto ignoriert `WITH TIES` und wertet die Abfrage als regulär aus `TOP` .
Kusto unterstützt nicht `PERCENT` .

## <a name="cursors"></a>Cursor

Kusto unterstützt keine SQL-Cursor.

## <a name="flow-control"></a>Flusssteuerung

Kusto unterstützt keine Fluss Steuerungs Anweisungen, mit Ausnahme einiger begrenzter Fälle, wie z `IF` `THEN` `ELSE` . b. einer-Klausel, die das identische Schema für die `THEN` Batches und aufweist `ELSE` .

## <a name="data-types"></a>Datentypen

Abhängig von der Abfrage haben die zurückgegebenen Daten möglicherweise einen anderen Typ als in SQL Server.
Ein offensichtliches Beispiel hierfür sind Typen wie `TINYINT` und `SMALLINT` , die in Kusto keine Entsprechung aufweisen. Daher kann es vorkommen, dass Clients, die einen Wert des Typs oder erwarten, `BYTE` `INT16` stattdessen einen- `INT32` oder-Wert erhalten `INT64` .

## <a name="column-order-in-results"></a>Spaltenreihenfolge in Ergebnissen

Wenn Asterix in der `SELECT` -Anweisung verwendet wird, kann die Reihenfolge der Spalten in den einzelnen Resultsets zwischen Kusto und SQL Server abweichen. Ein Client, der Spaltennamen verwendet, würde in diesen Fällen besser funktionieren.
Wenn in der Anweisung kein Asterix-Zeichen vorhanden ist `SELECT` , werden die Spalten Ordnungen beibehalten.

## <a name="columns-name-in-results"></a>Spaltenname in Ergebnissen

In T-SQL können mehrere Spalten denselben Namen aufweisen. Dies ist in Kusto nicht zulässig.
Bei einer Kollision in Namen können sich die Namen der Spalten in Kusto unterscheiden.
Der ursprüngliche Name wird jedoch beibehalten, zumindest für eine der Spalten.

## <a name="any-all-and-exists-predicates"></a>Beliebige, alle und vorhandene Prädikate

Kusto unterstützt die Prädikate `ANY` , `ALL` und nicht `EXISTS` .

## <a name="recursive-ctes"></a>Rekursive CTEs

Kusto unterstützt keine rekursiven allgemeinen Tabellen Ausdrücke.

## <a name="dynamic-sql"></a>Dynamische SQL-Anweisungen

Kusto unterstützt keine dynamischen SQL-Anweisungen (Inline Ausführung des von der Abfrage generierten SQL-Skripts).

## <a name="within-group"></a>WITHIN GROUP

Kusto unterstützt keine- `WITHIN GROUP` Klausel.

## <a name="truncate-function"></a>TRUNCATE-Funktion

Die TRUNCATE-Funktion (ODBC) in Kusto funktioniert ähnlich wie die Round, was bedeutet, dass das Ergebnis der nächste Wert und nicht der untere Wert ist, der in SQL zurückgegeben wird.