---
title: MS-TDS/T-SQL Unterschiede zwischen Kusto Microsoft SQL Server - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden MS-TDS/T-SQL-Unterschiede zwischen Kusto Microsoft SQL Server in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: be294053fdd0f95d488f52b547ef7abd0ef7c23c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523425"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>MS-TDS/T-SQL Unterschiede zwischen Kusto Microsoft SQL Server

Im Folgenden finden Sie eine unvollständige Liste der Hauptunterschiede zwischen Kusto und sql Servers Implementierung von T-SQL.

## <a name="create-insert-drop-alter-statements"></a>CREATE-, INSERT-, DROP-, ALTER-Anweisungen

Kusto unterstützt weder Schemaänderungen oder Datenänderungen über MS-TDS noch die oben genannten T-SQL-Anweisungen.

## <a name="correlated-sub-queries"></a>Korrelierte Unterabfragen

Kusto unterstützt keine korrelierten Unterabfragen `WHERE`in `JOIN` `SELECT`, und Klauseln.

## <a name="top-flavors"></a>TOP Aromen

Kusto ignoriert `WITH TIES` und wertet `TOP`Abfragen als reguläre aus.
Kusto unterstützt `PERCENT`nicht .

## <a name="cursors"></a>Cursor

Kusto unterstützt keine SQL-Cursor.

## <a name="flow-control"></a>Flusssteuerung

Kusto unterstützt keine Flow-Control-Anweisungen, mit Ausnahme einiger `IF` `THEN` `ELSE` begrenzter Fälle, z. B. einer Klausel, die das gleiche Schema für die `THEN` und `ELSE` Batches aufweist.

## <a name="data-types"></a>Datentypen

Je nach Abfrage können die zurückgegebenen Daten einen anderen Typ als in SQL Server haben.
Ein offensichtliches Beispiel hier `TINYINT` `SMALLINT` sind Typen wie und die in Kusto keine Entsprechung haben. Daher Clients, die einen `BYTE` Wert `INT16` des `INT32` Typs `INT64` erwarten oder stattdessen einen oder einen Wert abrufen.

## <a name="column-order-in-results"></a>Spaltenreihenfolge in Ergebnissen

Wenn asterix in `SELECT` der Anweisung verwendet wird, kann die Reihenfolge der Spalten in den einzelnen Resultsets zwischen Kusto und SQL Server variieren. Client, der Spaltennamen verwendet, würde in diesen Fällen besser funktionieren.
Wenn die `SELECT` Anweisung kein Asterixzeichen enthält, bleiben die Spaltenordinals erhalten.

## <a name="columns-name-in-results"></a>Spaltenname in Ergebnissen

In T-SQL können mehrere Spalten denselben Namen haben. Dies ist in Kusto nicht erlaubt.
Im Falle einer Kollision in Namen können die Namen der Spalten in Kusto unterschiedlich sein.
Der ursprüngliche Name bliebjedoch erhalten, zumindest für eine der Spalten.

## <a name="any-all-and-exists-predicates"></a>ANY-, ALL- und EXISTS-Prädikate

Kusto unterstützt die Prädikate `ANY`, `ALL`und `EXISTS`nicht.

## <a name="recursive-ctes"></a>Rekursive CTEs

Kusto unterstützt keine rekursiven allgemeinen Tabellenausdrücke.

## <a name="dynamic-sql"></a>Dynamische SQL-Anweisungen

Kusto unterstützt keine dynamischen SQL-Anweisungen (Inlineausführung von SQL-Skript, das von der Abfrage generiert wird).

## <a name="within-group"></a>WITHIN GROUP

Kusto unterstützt `WITHIN GROUP` keine Klausel.

## <a name="truncate-function"></a>TRUNCATE-Funktion

Die TRUNCATE-Funktion (ODBC) in Kusto funktioniert ähnlich wie ROUND, was bedeutet, dass das Ergebnis der nächste Wert ist und nicht der niedrigere, der in SQL zurückgegeben wird.