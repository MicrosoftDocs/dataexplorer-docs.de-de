---
title: 'Einschränkungs Anweisung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Einschränkungs Anweisung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f510e62e6b1ad828f0e132bbad214dc7119f8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243014"
---
# <a name="restrict-statement"></a>restrict-Anweisung

::: zone pivot="azuredataexplorer"

Die Einschränkungs Anweisung schränkt den Satz von Tabellen-/ansichtentitäten ein, die für die folgenden Abfrage Anweisungen sichtbar sind. In einer Datenbank, die zwei Tabellen ( `A` , `B` ) enthält, kann die Anwendung z. b. verhindern, dass der Rest der Abfrage auf eine begrenzte Form einer Tabelle zugreift, `B` `A` indem eine Sicht verwendet wird.

Das Hauptszenario der Einschränkungs Anweisung ist für Anwendungen der mittleren Ebene, die Abfragen von Benutzern akzeptieren und einen Sicherheitsmechanismus auf Zeilenebene über diese Abfragen anwenden möchten. Die Anwendung der mittleren Ebene kann der Abfrage des Benutzers ein **Logisches Modell**, eine Reihe von Let-Anweisungen, die Ansichten definieren, die den Zugriff des Benutzers auf Daten einschränken (z `T | where UserId == "..."` . b.). Die letzte hinzugefügte Anweisung schränkt den Zugriff des Benutzers auf das logische Modell ein.

> [!NOTE]
> Die Einschränkungs Anweisung kann verwendet werden, um den Zugriff auf Entitäten in einer anderen Datenbank oder in einem Cluster einzuschränken (Platzhalter werden in Cluster Namen nicht unterstützt).

## <a name="syntax"></a>Syntax

`restrict``access` `to` `(` [*Entityspecifier* [ `,` ...]]`)`

Where *entityspecifier* ist einer der folgenden:
* Ein Bezeichner, der durch eine Let-Anweisung als tabellarische Ansicht definiert ist.
* Ein Tabellen Verweis (ähnlich wie bei einer Union-Anweisung).
* Ein Muster, das durch eine Muster Deklaration definiert wird.

Alle Tabellen, Tabellen Sichten oder Muster, die nicht von der Einschränkungs Anweisung angegeben werden, werden für den Rest der Abfrage "unsichtbar". 

## <a name="arguments"></a>Argumente

Die Einschränkungs Anweisung kann einen oder mehrere Parameter abrufen, die die Einschränkungs Einschränkung während der Namensauflösung der Entität definieren. Die Entität kann folgende sein:
* [Let-Anweisung](./letstatement.md) wird vor der- `restrict` Anweisung angezeigt. 

  ```kusto
  // Limit access to 'Test' let statement only
  let Test = () { print x=1 };
  restrict access to (Test);
  ```

* [Tabellen](../management/tables.md) oder [Funktionen](../management/functions.md) , die in den Daten Bank Metadaten definiert sind.

    ```kusto
    // Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
    // and other database 'DB2' has Table2 defined in the metadata
    
    restrict access to (database().Table1, database().Func1, database('DB2').Table2);
    ```

* Platzhalter Muster, die vielfachen von [Let-Anweisungen](./letstatement.md) oder Tabellen/Funktionen entsprechen können  

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

## <a name="examples"></a>Beispiele

Im folgenden Beispiel wird gezeigt, wie eine Anwendung der mittleren Ebene einem Benutzer eine Abfrage mit einem logischen Modell voranstellt, das verhindert, dass der Benutzer andere Benutzerdaten abfragt.

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

// Restricting access to Table1 in the current database and Table2 in database 'DB2'
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

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
