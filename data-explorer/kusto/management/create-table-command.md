---
title: '. Create Table: Azure-Daten-Explorer'
description: In diesem Artikel wird die CREATE TABLE-Tabelle in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 8cfdbe1420745620fcaaf6af81e4f750ca25c1cd
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321606"
---
# <a name="create-table"></a>.create table

Erstellt eine neue leere Tabelle.

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden.

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax**

`.create``table` *TableName* ([columnName: ColumnType],...)  [ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*] `)` ]

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
> Verwenden Sie zum Erstellen mehrerer Tabellen den [`.create tables`](create-tables-command.md) -Befehl, um die Leistung zu verbessern und die Auslastung des Clusters zu verringern.
