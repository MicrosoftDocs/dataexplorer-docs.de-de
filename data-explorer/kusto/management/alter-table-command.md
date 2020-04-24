---
title: .alter-Tabelle und .alter-merge-Tabelle - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden die .alter-Tabelle und die .alter-merge-Tabelle in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 516c5c7b85f7c310188fd11ae24e52cb23423143
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522371"
---
# <a name="alter-table-and-alter-merge-table"></a>.alter-Tabelle und .alter-merge-Tabelle

Der `.alter table` Befehl legt ein neues Spaltenschema, eine neue Dokumentzeichenfolge und einen neuen Ordner in eine vorhandene Tabelle fest und überschreibt das vorhandene Spaltenschema, die Dokumentation und den Ordner. Daten in vorhandenen Spalten, die durch den Befehl "erhalten" werden, werden beibehalten (so kann dieser Befehl z. B. verwendet werden, um die Spalten einer Tabelle neu anzuordnen).

Der `.alter-merge table` Befehl fügt einer vorhandenen Tabelle neue Spalten, Docstring und Ordner hinzu.
Daten in vorhandenen Spalten bleiben erhalten.

Beide Befehle müssen im Kontext einer bestimmten Datenbank ausgeführt werden, die den Tabellennamen umgibt.

Erfordert [die Berechtigung "Table Admin"](../management/access-control/role-based-authorization.md).

**Syntax**

`.alter``table` *TableName* (*columnName*:*columnType*, ...)  [`with` `(``docstring` `=` [ *Dokumentation*`,` `folder` `=` ] `)`[ *Ordnername*] ]

`.alter-merge``table` *TableName* (*columnName*:*columnType*, ...)  [`with` `(``docstring` `=` [ *Dokumentation*`,` `folder` `=` ] `)`[ *Ordnername*] ]

Geben Sie die Spalten an, die die Tabelle nach erfolgreichem Abschluss haben soll. 

> [!WARNING]
> Die `.alter` falsche Verwendung des Befehls kann zu Datenverlust führen.
> Lesen Sie die `.alter` `.alter-merge` Unterschiede zwischen und unten sorgfältig durch.

`.alter-merge`:

 * Spalten, die nicht vorhanden sind und die Sie angeben, werden am Ende des vorhandenen Schemas hinzugefügt.
 * Wenn das übergebene Schema einige Tabellenspalten nicht enthält, werden sie nicht gelöscht.
 * Wenn Sie eine vorhandene Spalte mit einem anderen Typ angegeben haben, schlägt der Befehl fehl.

`.alter`nur (nicht): `.alter-merge`

 * Die Tabelle hat genau die gleichen Spalten in der gleichen Reihenfolge wie angegeben.
 * Vorhandene Spalten, die nicht im Befehl angegeben `.drop column`sind, werden gelöscht (wie in ) und daten in ihnen gehen verloren.
 * Das Ändern eines Spaltentyps wird beim Ändern einer Tabelle nicht unterstützt. Verwenden Sie stattdessen den Spaltenbefehl [.alter.](alter-column.md)

> [!TIP] 
> Verwenden `.show table [TableName] cslschema` Sie diese Datei, um das vorhandene Spaltenschema abzudateien, bevor Sie es ändern. 

Für beide Befehle gilt:

1. Vorhandene Daten werden durch diese Befehle nicht physisch geändert. Daten in entfernten Spalten werden ignoriert. Daten in neuen Spalten werden als null angenommen.
1. Je nachdem, wie der Cluster konfiguriert ist, kann die Datenerfassung das Spaltenschema der Tabelle ändern, auch ohne Benutzerinteraktion. Stellen Sie daher beim Vornehmen von Änderungen am Spaltenschema einer Tabelle sicher, dass die Aufnahme keine erforderlichen Spalten hinzufügt, die der Befehl dann entfernt.

> [!WARNING]
> Datenerfassungsprozesse in die Tabelle, die das Spaltenschema der Tabelle `.alter table` ändern, die parallel mit dem Befehl auftreten, können agnostisch in der Reihenfolge der Tabellenspalten ausgeführt werden. Es besteht auch die Gefahr, dass Daten in die falschen Spalten aufgenommen werden. Verhindern Sie dies, indem Sie die Aufnahme während solcher Befehle beenden oder indem Sie sicherstellen, dass solche Aufnahmevorgänge immer ein Zuordnungsobjekt verwenden.

**Beispiele**

```
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```