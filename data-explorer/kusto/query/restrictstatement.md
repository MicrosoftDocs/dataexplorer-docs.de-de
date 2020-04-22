---
title: Restrict-Anweisung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Restrict-Anweisung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: cbd21c01956f817c5db19a93104028dba2b2b2b4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765994"
---
# <a name="restrict-statement"></a>restrict-Anweisung

::: zone pivot="azuredataexplorer"

Die restrict-Anweisung schränkt den Satz von Tabellen-/Ansichtsentitäten ein, die für Abfrageanweisungen sichtbar sind, die darauf folgen. In einer Datenbank, die z.`A` `B`B. zwei Tabellen ( , ) `B` enthält, kann die Anwendung verhindern, dass der Rest der Abfrage auf eine eingeschränkte Form von Tabellen `A` zugreift, indem sie eine Ansicht verwendet.

Das Hauptszenario der restrict-Anweisung ist für Anwendungen der mittleren Ebene, die Abfragen von Benutzern akzeptieren und einen Sicherheitsmechanismus auf Zeilenebene auf diese Abfragen anwenden möchten. Die Anwendung der mittleren Ebene kann der Abfrage des Benutzers ein **logisches Modell**voranstellen, eine Reihe von `T | where UserId == "..."`let-Anweisungen, die Ansichten definieren, die den Zugriff des Benutzers auf Daten einschränken (z. B. ). Als letzte Anweisung, die hinzugefügt wird, schränkt sie den Zugriff des Benutzers auf das logische Modell ein.

**Syntax**

`restrict``access` `,` [ EntitySpecifier [ ...]]*EntitySpecifier* `to` `(``)`

Wobei *EntitySpecifier* einer der wichtigsten ist:
* Ein Bezeichner, der durch eine let-Anweisung als Tabellarische Ansicht definiert wird.
* Ein Tabellenverweis (ähnlich wie ein, der von einer Union-Anweisung verwendet wird).
* Ein Muster, das durch eine Musterdeklaration definiert ist.

Alle Tabellen, Tabellenansichten oder Muster, die nicht durch die Restrict-Anweisung angegeben werden, werden für den Rest der Abfrage "unsichtbar". 

**Hinweise**

Die restrict-Anweisung kann verwendet werden, um den Zugriff auf Entitäten in einer anderen Datenbank oder einem anderen Cluster einzuschränken (Platzhalter werden in Clusternamen nicht unterstützt).

**Argumente**

Die restrict-Anweisung kann einen oder mehrere Parameter abrufen, die die zulässige Einschränkung während der Namensauflösung der Entität definieren. Die Entität kann sein:
- [lassen Sie](./letstatement.md) `restrict` Anweisung vor der Anweisung erscheinen. 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- [Tabellen](../management/tables.md) oder [Funktionen,](../management/functions.md) die in den Datenbankmetadaten definiert sind.

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- Platzhaltermuster, die mit Mehreren von [let-Anweisungen](./letstatement.md) oder Tabellen/Funktionen übereinstimmen können  

```kusto
let Test1 = () { print x=1 };
let Test2 = () { print y=1 };
restrict access to (*);
// Now access is restricted to Test1, Test2 and no tables/functions are accessible.

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database().*);
// Now access is restricted to all tables/functions of the current database ('DB2' is not accessible).

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database('DB2').*);
// Now access is restricted to all tables/functions of the database 'DB2'
```


**Beispiele**

Das folgende Beispiel zeigt, wie eine Anwendung der mittleren Ebene der Abfrage eines Benutzers ein logisches Modell voranstellen kann, das verhindert, dass der Benutzer die Daten eines anderen Benutzers abfragt.

```kusto
// Assume the database has a single table, UserData,
// with a column called UserID and other columns that hold
// per-user private information.
//
// The middle-tier application generates the following statements.
// Note that "username@domain.com" is something the middle-tier application
// derives per-user as it authenticates the user.
let RestrictedData = view () { Data | where UserID == "username@domain.com" };
restrict access to (RestrictedData);
// The rest of the query is something that the user types.
// This part can only reference RestrictedData; attempting to reference Data
// will fail.
RestrictedData | summarize IrsLovesMe=sum(Salary) by Year, Month
```

```kusto
// Restricting access to Table1 in the current database (database() called without parameters)
restrict access to (database().Table1);
Table1 | count

// Restricting acess to Table1 in the current database and Table2 in database 'DB2'
restrict access to (database().Table1, database('DB2').Table2);
union 
    (Table1),
    (database('DB2').Table2))
| count

// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
