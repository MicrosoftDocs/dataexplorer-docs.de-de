---
title: '. Create Table: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die CREATE TABLE-Tabelle in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 25554b5485562179d849e846fc5e71c587815e86
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108421"
---
# <a name="create-table"></a>.create table

Erstellt eine neue leere Tabelle.

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden.

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax**

`.create``table` *TableName* ([columnName: ColumnType],...)  [`with` `(`[`docstring` `=` *Documentation*`,` *FolderName* `)`Dokumentation] [ `folder` FolderName]] `=`

Wenn die Tabelle bereits vorhanden ist, wird der Befehl erfolgreich zurückgegeben.

**Beispiel** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**Ausgabe zurückgeben**

Gibt das Schema der Tabelle im JSON-Format zurück, wie folgt:

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> Verwenden Sie zum Erstellen mehrerer Tabellen den Befehl [. Create Tables](create-tables-command.md) , um die Leistung zu verbessern und die Auslastung des Clusters zu verringern.

## <a name="create-merge-table"></a>. Create-MERGE-Tabelle

Erstellt eine neue Tabelle oder erweitert eine vorhandene Tabelle. 

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden. 

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax**

`.create-merge``table` *TableName* ([columnName: ColumnType],...)  [`with` `(`[`docstring` `=` *Documentation*`,` *FolderName* `)`Dokumentation] [ `folder` FolderName]] `=`

Wenn die Tabelle nicht vorhanden ist, funktioniert genau wie der Befehl ". Create Table".

Wenn Tabelle t vorhanden ist, und Sie den Befehl ". Create-Merge Table T<columns specification>()" senden, wird Folgendes angezeigt:

* Alle Spalten in <columns specification> , die zuvor nicht in t vorhanden waren, werden am Ende des t-Schemas hinzugefügt.
* Alle Spalten in t, die nicht in <columns specification> vorhanden sind, werden nicht aus t entfernt.
* Jede Spalte in <columns specification> , die in T vorhanden ist, aber mit einem anderen Datentyp, bewirkt, dass der Befehl fehlschlägt.
