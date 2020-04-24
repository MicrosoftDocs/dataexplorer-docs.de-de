---
title: Aufbewahrungsrichtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Aufbewahrungsrichtlinie in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: b0366bef619d815bbe58f91730eff70ec847c239
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520348"
---
# <a name="retention-policy"></a>Aufbewahrungsrichtlinie

In diesem Artikel werden Steuerbefehle beschrieben, die zum Erstellen und Ändern von [Aufbewahrungsrichtlinien](retentionpolicy.md)verwendet werden.

## <a name="show-retention-policy"></a>Aufbewahrungsrichtlinie anzeigen

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: Tabelle oder Datenbank
* `database_or_table`: `database_name` `database_name.table_name` oder `table_name` oder (im Datenbankkontext)

**Beispiel**

Die Aufbewahrungsrichtlinie für `MyDatabase`die Datenbank mit dem Namen :

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>Aufbewahrungsrichtlinie löschen

Das Löschen der Datenaufbewahrungsrichtlinie ist eine unbedenkliche Datenaufbewahrung.

Wenn Sie die Datenaufbewahrungsrichtlinie der Tabelle löschen, leitet die Tabelle die Aufbewahrungsrichtlinie von der Datenbankebene ab.

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: Tabelle oder Datenbank
* `database_or_table`: `database_name` `database_name.table_name` oder `table_name` oder (im Datenbankkontext)

**Beispiel**

Löschen Sie die Aufbewahrungsrichtlinie für die Tabelle mit dem Namen `MyTable1`:

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>Änderung der Aufbewahrungsrichtlinie

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: Tabelle oder Datenbank
* `database_or_table`: `database_name` `database_name.table_name` oder `table_name` oder (im Datenbankkontext)
* `table_name`: Name einer Tabelle in einem Datenbankkontext.  Ein Platzhalter (ist`*` hier zulässig).
* `retention_policy` :

```
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**Beispiele**

Die Aufbewahrungsrichtlinie für `MyDatabase`die Datenbank mit dem Namen :

```kusto
.show database MyDatabase policy retention
```

Legt eine Aufbewahrungsrichtlinie mit einem 10-tägigen Soft-Löschzeitraum und deaktivierter Datenwiederherstellungsfähigkeit fest:

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

Legt eine Aufbewahrungsrichtlinie mit einem 10-tägigen Soft-Lösch-Zeitraum und aktivierter Datenwiederherstellungsfähigkeit fest:

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Legt die gleiche Aufbewahrungsrichtlinie wie oben fest, aber dieses Mal für mehrere Tabellen (Tabelle1, Tabelle2 und Tabelle3):

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Legt eine Aufbewahrungsrichtlinie mit den Standardwerten fest: 100 Jahre als Soft-Löschzeitraum und aktivierte Wiederherstellbarkeit:

```kusto
.alter table Table1 policy retention "{}"
```