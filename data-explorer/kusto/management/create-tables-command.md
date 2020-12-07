---
title: '. Create Tables: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Tabellen in Azure Daten-Explorer erstellt werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 28ee7e462245497dd14d3a14a1c0703cc71a8933
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321589"
---
# <a name="create-tables"></a>.create tables

Erstellt neue leere Tabellen als Massen Vorgang.

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden.

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax**

`.create``tables` *TableName1* ([columnName: ColumnType],...) [ `,` *TableName2* ([columnName: ColumnType],...)...] [ `with` `(` `folder` `=` *FolderName*] `)` ]

Wenn bereits eine Tabelle vorhanden ist, wird der Befehl erfolgreich zurückgegeben.
 
**Beispiel** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
