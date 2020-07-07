---
title: '. Create Tables: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Tabellen in Azure Daten-Explorer erstellt werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: ff8b3cfae6d3364b4d094f588c8130761fa5cb31
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966991"
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
