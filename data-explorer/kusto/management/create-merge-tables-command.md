---
title: . Create-Merge Tables-Azure Daten-Explorer
description: Dieser Artikel beschreibt den Befehl. Create-Merge Tables in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f80ea54ece66440dc7a6b40d9d571f04bd3e26b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011515"
---
# <a name="create-merge-tables"></a>. Create-Tabellen zusammenführen

Ermöglicht es Ihnen, die Schemas vorhandener Tabellen in einem einzelnen Massen Vorgang im Kontext einer bestimmten Datenbank zu erstellen und zu erweitern.

> [!NOTE]
> Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).
> Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md) zum Erweitern vorhandener Tabellen.

**Syntax**

`.create-merge``tables` *TableName1* ([columnName: ColumnType],...) [ `,` *TableName2* ([columnName: ColumnType],...)...]

* Angegebene Tabellen, die nicht vorhanden sind, werden erstellt.
* Für angegebene Tabellen, die bereits vorhanden sind, werden die Schemas erweitert.
    * Nicht vorhandene Spalten werden am _Ende_ des Schemas der vorhandenen Tabelle hinzugefügt.
    * Vorhandene Spalten, die nicht im Befehl angegeben sind, werden nicht aus dem Schema der vorhandenen Tabelle entfernt.
    * Vorhandene Spalten, die mit einem Datentyp im Befehl angegeben werden, der sich von dem in den Schemas der vorhandenen Tabelle unterscheidet, führen zu einem Fehler. Es werden keine Tabellen erstellt oder erweitert.

**Beispiel**

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**Ausgabe zurückgeben**

| TableName | DatabaseName  | Ordner | DocString |
|-----------|---------------|--------|-----------|
| MyLogs    | Topcomparison |        |           |
| Myusers   | Topcomparison |        |           |
