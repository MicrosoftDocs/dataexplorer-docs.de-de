---
title: .create-Tabelle - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die .create-Tabelle in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 1c84b9b6cec5a5bb7ab620871f59c188bf71cef4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744241"
---
# <a name="create-table"></a>.create table

Erstellt eine neue leere Tabelle.

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden.

Erfordert [Datenbankbenutzerberechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.create``table` *TableName* ([columnName:columnType], ...)  [`with` `(``docstring` `=` [ *Dokumentation*`,` `folder` `=` ] `)`[ *Ordnername*] ]

Wenn die Tabelle bereits vorhanden ist, gibt der Befehl erfolg.

**Beispiel** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**Return-Ausgabe**

Gibt das Schema der Tabelle im JSON-Format zurück, wie:

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> Verwenden Sie zum Erstellen mehrerer Tabellen den Befehl [.create tables](/create-tables.md) für eine bessere Leistung und eine geringere Auslastung des Clusters.

## <a name="create-merge-table"></a>.create-merge-Tabelle

Erstellt eine neue Tabelle oder erweitert eine vorhandene Tabelle. 

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden. 

Erfordert [Datenbankbenutzerberechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.create-merge``table` *TableName* ([columnName:columnType], ...)  [`with` `(``docstring` `=` [ *Dokumentation*`,` `folder` `=` ] `)`[ *Ordnername*] ]

Wenn die Tabelle nicht vorhanden ist, funktioniert sie genau als Befehl ".create table".

Wenn Tabelle T vorhanden ist und Sie einen Befehl<columns specification>".create-merge table T ( )" senden, dann:

* Jede Spalte, in <columns specification> der zuvor in T nicht vorhanden war, wird am Ende des T-Schemas hinzugefügt.
* Jede Spalte in T, <columns specification> die sich nicht in befindet, wird nicht aus T entfernt.
* Jede Spalte <columns specification> in T, aber mit einem anderen Datentyp führt dazu, dass der Befehl fehlschlägt.
