---
title: .undo-Ablagetabelle - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die .undo-Drop-Tabelle in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 55fd08ce2624c522b971e1ee3862f7d11c6f679f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519549"
---
# <a name="undo-drop-table"></a>.undo drop table

`.undo` `drop` Der `table` Befehl stellt einen Ablagetabellenvorgang auf eine bestimmte Datenbankversion zurück.

**Syntax**

`.undo``drop` *TableName* `as` `version=v` *DB_MajorVersion.DB_MinorVersion* *NewTableName*Tabellenname [ NewTableName ] DB_MajorVersion.DB_MinorVersion `table`

Der Befehl muss mit Datenbankkontext ausgeführt werden.

**Rückgabe**

Dieser Befehl:
* Gibt die ursprüngliche Tabellenausdehnungsliste zurück
* Gibt für jede Ausdehnung die Anzahl der Datensätze an, die die Ausdehnung enthält.
* Gibt zurück, wenn der Wiederherstellungsvorgang erfolgreich war oder fehlgeschlagen ist
* Gibt ggf. den Fehlergrund zurück.

| ExtentId                             | NumberOfRecords | Status                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | Wiederhergestellt                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | Wiederhergestellt                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | Ausdehnung nicht wiederhergestellt werden können | Ausdehnungscontainer: 4b47fd84-c7db-4cfb-9378-67c1de7bf154 wurde nicht gefunden, die Ausdehnung wurde aus dem Speicher entfernt und kann nicht wiederhergestellt werden |

**Beispiele**

```
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**So finden Sie die erforderliche Datenbankversion**

Sie können die Datenbankversion finden, bevor der `.show` `journal` Drop-Vorgang ausgeführt wurde, indem Sie den Befehl :

```
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| OriginalEntityVersion |
|-----------------------|
| v24.3                 |

**Einschränkungen**

Wenn ein Befehl "Bereinigen" in dieser Datenbank ausgeführt wurde, kann der Befehl "Drop-Tabellen rückgängig machen" nicht in einer Version früher zur Löschausführung ausgeführt werden.

Die Ausdehnung kann nur wiederhergestellt werden, wenn der harte Löschzeitraum des Ausdehnungscontainers, in dem er sich befindet, noch nicht erreicht wurde.

Der Befehl erfordert [die Berechtigung des Datenbankadministrators](../management/access-control/role-based-authorization.md).