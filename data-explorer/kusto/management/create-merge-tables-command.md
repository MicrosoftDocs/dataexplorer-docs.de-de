---
title: .create-merge-Tabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden .create-merge-Tabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 408046e198710c4b825a399fcb90960411de1041
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744444"
---
# <a name="create-merge-tables"></a>.create-merge-Tabellen

Ermöglicht das Erstellen und/oder Erweitern der Schemas vorhandener Tabellen in einem einzelnen Massenvorgang im Kontext einer bestimmten Datenbank.

Erfordert [Datenbankbenutzerberechtigung](../management/access-control/role-based-authorization.md)sowie [Tabellenadministratorberechtigung](../management/access-control/role-based-authorization.md) zum Erweitern vorhandener Tabellen.

**Syntax**

`.create-merge``tables` *TableName1* ([columnName:columnType], ...) [`,` *TableName2* ([columnName:columnType], ...) ... ]

* Es werden angegebene Tabellen erstellt, die nicht vorhanden sind.
* Bei den bereits vorhandenen Tabellen werden die Schemas erweitert:
    * Nicht vorhandene Spalten werden am _Ende_ des Schemas der vorhandenen Tabelle hinzugefügt.
    * Vorhandene Spalten, die nicht im Befehl angegeben sind, werden nicht aus dem Schema der vorhandenen Tabelle entfernt.
    * Vorhandene Spalten, die mit einem anderen Datentyp im Befehl als im Schema der vorhandenen Tabelle angegeben sind, führen zu einem Fehler (es werden keine Tabellen erstellt oder erweitert).

**Beispiel** 

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**Return-Ausgabe**

| TableName | DatabaseName  | Ordner | DocString |
|-----------|---------------|--------|-----------|
| MyLogs    | TopComparison |        |           |
| MyUsers   | TopComparison |        |           |
