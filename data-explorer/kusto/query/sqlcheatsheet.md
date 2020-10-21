---
title: 'SQL-zu-Kusto-Abfrage Übersetzung: Azure Daten-Explorer'
description: In diesem Artikel wird die SQL-zu-Kusto-Abfrage Übersetzung in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 216d8c0eeacf6733eb1f7d4b4880bbad1d408e02
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247086"
---
# <a name="sql-to-kusto-cheat-sheet"></a>Cheat Sheet für die Übersetzung von SQL in Kusto

Kusto unterstützt eine Teilmenge der SQL-Sprache. Eine vollständige Liste der nicht unterstützten Funktionen finden Sie in der Liste der [bekannten SQL-Probleme](../api/tds/sqlknownissues.md) .

Die primäre Sprache für die Interaktion mit Kusto ist kql (Kusto Query Language). Um das Übergangs-und Lernverfahren zu vereinfachen, können Sie Kusto verwenden, um SQL-Abfragen in kql zu übersetzen. Senden Sie eine SQL-Abfrage an Kusto, und stellen Sie Ihr das Verb "EXPLAIN" vorangestellt.

Beispiel:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
EXPLAIN 
SELECT COUNT_BIG(*) as C FROM StormEvents 
```

|Abfrage|
|---|
|StormEvents<br>| C = count () zusammenfassen<br>| Projekt C|

## <a name="sql-to-kusto-cheat-sheet"></a>Cheat Sheet für die Übersetzung von SQL in Kusto

Die folgende Tabelle zeigt Beispielabfragen in SQL und ihre kql-Entsprechungen.

|Category |SQL-Abfrage |Kusto-Abfrage
|---|---|---
Auswählen von Daten aus einer Tabelle |<code>SELECT * FROM dependencies</code> | <code>dependencies</code>
--|<code>SELECT name, resultCode FROM dependencies</code> |<code>dependencies &#124; project name, resultCode</code>
--|<code>SELECT TOP 100 * FROM dependencies</code> | <code>dependencies &#124; take 100</code>
Null-Auswertung |<code>SELECT * FROM dependencies<br>WHERE resultCode IS NOT NULL</code> | <code>dependencies<br>&#124; where isnotnull(resultCode)</code>
Vergleichs Operatoren (Datum) |<code>SELECT * FROM dependencies<br>WHERE timestamp > getdate()-1</code>| <code>dependencies<br>&#124; where timestamp > ago(1d)</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp BETWEEN ... AND ...</code> |<code>dependencies<br>&#124; where timestamp > datetime(2016-10-01)<br>&nbsp;&nbsp;and timestamp <= datetime(2016-11-01)</code>
Vergleichs Operatoren (Zeichenfolge)|<code>SELECT * FROM dependencies<br>WHERE type = "Azure blob"</code> |<code>dependencies<br>&#124; where type == "Azure blob"</code>
--|<code>-- substring<br>SELECT * FROM dependencies<br>WHERE type like "%blob%"</code> |<code>// substring<br>dependencies<br>&#124; where type contains "blob"</code>
--|<code>-- wildcard<br>SELECT * FROM dependencies<br>WHERE type like "Azure%"</code> |<code>// wildcard<br>dependencies<br>&#124; where type startswith "Azure"<br>// or<br>dependencies<br>&#124; where type matches regex "^Azure.*"</code>
Vergleich (Boolean) |<code>SELECT * FROM dependencies<br>WHERE !(success)</code> |<code>dependencies<br>&#124; where success == "False"</code>
Distinct |<code>SELECT DISTINCT name, type  FROM dependencies</code> |<code>dependencies<br>&#124; summarize by name, type</code>
Gruppierung, Aggregation |<code>SELECT name, AVG(duration) FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize avg(duration) by name</code>
Spalten Aliase, erweitern |<code>SELECT operationName as Name, AVG(duration) as AvgD FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize AvgD = avg(duration) by operationName<br>&#124; project Name = operationName, AvgD</code>
Sortieren |<code>SELECT name, timestamp FROM dependencies<br>ORDER BY timestamp ASC</code> |<code>dependencies<br>&#124; project name, timestamp<br>&#124; order by timestamp asc nulls last</code>
Top n nach Measure |<code>SELECT TOP 100 name, COUNT(*) as Count FROM dependencies<br>GROUP BY name<br>ORDER BY Count DESC</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; top 100 by Count desc</code>
Union |<code>SELECT * FROM dependencies<br>UNION<br>SELECT * FROM exceptions</code> |<code>union dependencies, exceptions</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp > ...<br>UNION<br>SELECT * FROM exceptions<br>WHERE timestamp > ...</code> |<code>dependencies<br>&#124; where timestamp > ago(1d)<br>&#124; union<br>&nbsp;&nbsp;(exceptions<br>&nbsp;&nbsp;&#124; where timestamp > ago(1d))</code>
Join |<code>SELECT * FROM dependencies <br>LEFT OUTER JOIN exception<br>ON dependencies.operation_Id = exceptions.operation_Id</code> |<code>dependencies<br>&#124; join kind = leftouter<br>&nbsp;&nbsp;(exceptions)<br>on $left.operation_Id == $right.operation_Id</code>
Geschachtelte Abfragen |<code>SELECT * FROM dependencies<br>WHERE resultCode == <br>(SELECT TOP 1 resultCode FROM dependencies<br>WHERE resultId = 7<br>ORDER BY timestamp DESC)</code> |<code>dependencies<br>&#124; where resultCode == toscalar(<br>&nbsp;&nbsp;dependencies<br>&nbsp;&nbsp;&#124; where resultId == 7<br>&nbsp;&nbsp;&#124; top 1 by timestamp desc<br>&nbsp;&nbsp;&#124; project resultCode)</code>
Muss |<code>SELECT COUNT(\*) FROM dependencies<br>GROUP BY name<br>HAVING COUNT(\*) > 3</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; where Count > 3</code>|
