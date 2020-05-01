---
title: . Undo DROP TABLE-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie DROP TABLE in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2682afaa9e589a8787b7e42a83e3bb68a24a2781
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616676"
---
# <a name="undo-drop-table"></a>.undo drop table

Mit `.undo` `drop` `table` dem Befehl wird ein DROP TABLE-Vorgang auf eine bestimmte Datenbankversion zurückgesetzt.

**Syntax**

`.undo``drop` `version=v` *DB_MajorVersion.DB_MinorVersion* *TableName* `as` *NewTableName*TableName [newtablename] DB_MajorVersion. DB_MinorVersion `table`

Der Befehl muss mit dem Daten Bank Kontext ausgeführt werden.

**Rückgabe**

Dieser Befehl:
* Gibt die ursprüngliche Tabellen Erweiterungs Liste zurück.
* Gibt für jeden Block die Anzahl der Datensätze an, die der Block enthält.
* Gibt zurück, wenn der Wiederherstellungs Vorgang erfolgreich war oder nicht
* Gibt ggf. den Grund für den Fehler zurück.

| Extentid                             | Zahlderdatensätze | Status                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | Erhol                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | Erhol                |
| 861f 5-6a CDE-4f 1E-A003-a43506 | 855             | Der Block kann nicht wieder hergestellt werden | Block Container: 4b47fd84-c7db-4cfb-9378-67c1de7bf154 wurde nicht gefunden, der Block wurde aus dem Speicher entfernt und kann nicht wieder hergestellt werden. |

**Beispiele**

```kusto
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```kusto
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**Ermitteln der erforderlichen Datenbankversion**

Mithilfe des `.show` `journal` folgenden Befehls können Sie die Datenbankversion vor der Ausführung des Drop-Vorgangs ermitteln:

```kusto
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| Originalentityversion |
|-----------------------|
| v 24,3                 |

**Einschränkungen**

Wenn ein Löschbefehl für diese Datenbank ausgeführt wurde, kann der Befehl "DROP TABLE rückgängig machen" nicht in einer früheren Version der Lösch Ausführung ausgeführt werden.

Der Wert kann nur wieder hergestellt werden, wenn der harte Lösch Zeitraum des Block Containers, in dem er sich befindet, noch nicht erreicht wurde.

Der Befehl erfordert die [Administrator Berechtigung "Database](../management/access-control/role-based-authorization.md)".
