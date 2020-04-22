---
title: .create-Tabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden .create-Tabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f76c4d4a2780b58d9596537aea183d026d086fc5
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744032"
---
# <a name="create-tables"></a>.create tables

Erstellt neue leere Tabellen als Massenvorgang.

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden.

Erfordert [Datenbankbenutzerberechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.create``tables` *TableName1* ([columnName:columnType], ...) [`,` *TableName2* ([columnName:columnType], ...) ... ]

Wenn bereits eine Tabelle vorhanden ist, gibt der Befehl den Erfolg zurück.
 
**Beispiel** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
