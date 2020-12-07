---
title: '. Alter-MERGE-Tabelle: Azure-Daten-Explorer'
description: In diesem Artikel wird der Befehl ". Alter-Merge Table" beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: 1a58d44e7884fb198f04a9f12a71c77cf164331b
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321691"
---
# <a name="alter-merge-table"></a>.alter-merge table
 
Mit dem Befehl `.alter-merge table`:

* Sichert Daten in vorhandenen Spalten.
* Fügt `docstring` einer vorhandenen Tabelle neue Spalten, und Ordner hinzu.
* Muss im Kontext einer bestimmten Datenbank ausgeführt werden, in der der Tabellenname festgelegt ist.
* Erfordert die [Berechtigung "Table admin](../management/access-control/role-based-authorization.md) "

> [!WARNING]
> Wenn Sie den `.alter-merge` Befehl falsch verwenden, kann dies zu Datenverlusten führen.

> [!TIP]
> Der `.alter-merge` verfügt über eine Entsprechung, den `.alter` Tabellen Befehl, der über eine ähnliche Funktionalität verfügt. Weitere Informationen finden Sie unter [`.alter table`](../management/alter-table-command.md).

**Syntax**

`.alter-merge``table` *TableName* (*ColumnName*:*ColumnType*,...)  [ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*] `)` ]

Tabellen Spalten angeben:
 * Spalten, die nicht vorhanden sind und die Sie angeben, werden am Ende des vorhandenen Schemas hinzugefügt.
 * Wenn das bestandene Schema keine Tabellen Spalten enthält, werden die Spalten nicht gelöscht.
 * Wenn Sie eine vorhandene Spalte mit einem anderen Typ angeben, schlägt der Befehl fehl.

> [!TIP]
> Verwenden Sie `.show table [TableName] cslschema` , um das vorhandene Spalten Schema zu erhalten, bevor Sie es ändern.

Wie wirkt sich der Befehl auf die Daten aus?
* Vorhandene Daten werden vom Befehl nicht physisch geändert. Daten in entfernten Spalten werden ignoriert. Es wird davon ausgegangen, dass Daten in neuen Spalten NULL sind.
* Abhängig von der Konfiguration des Clusters kann die Datenerfassung das Spalten Schema der Tabelle ändern, auch ohne Benutzerinteraktion. Wenn Sie Änderungen am Spalten Schema einer Tabelle vornehmen, müssen Sie sicherstellen, dass die Erfassung erforderliche Spalten nicht hinzufügt, die der Befehl dann entfernt.

**Beispiele**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
