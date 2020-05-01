---
title: . ALTER TABLE und. Alter-Merge Table-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Tabelle "ALTER TABLE" und ". Alter-Merge" in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 18b0502754e95ca56d6a7f6946b9330bd42b174a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616438"
---
# <a name="alter-table-and-alter-merge-table"></a>. ALTER TABLE-und Alter-MERGE-Tabelle

Mit `.alter table` dem Befehl werden ein neues Spalten Schema, eine Zeichenfolge und ein neuer Ordner auf eine vorhandene Tabelle festgelegt, wobei das vorhandene Spalten Schema, die DocString und der Ordner überschrieben werden. Daten in vorhandenen Spalten, die vom Befehl "beibehalten" werden, werden beibehalten (daher kann dieser Befehl beispielsweise verwendet werden, um die Spalten einer Tabelle neu zu ordnen).

Der `.alter-merge table` Befehl fügt einer vorhandenen Tabelle neue Spalten, DocString und Folder, hinzu.
Daten in vorhandenen Spalten werden beibehalten.

Beide Befehle müssen im Kontext einer bestimmten Datenbank ausgeführt werden, die den Tabellennamen eingibt.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.alter``table` *TableName* (*ColumnName*:*ColumnType*,...)  [`with` `(`[`docstring` `=` *Documentation*`,` *FolderName* `)`Dokumentation] [ `folder` FolderName]] `=`

`.alter-merge``table` *TableName* (*ColumnName*:*ColumnType*,...)  [`with` `(`[`docstring` `=` *Documentation*`,` *FolderName* `)`Dokumentation] [ `folder` FolderName]] `=`

Geben Sie die Spalten an, die die Tabelle nach erfolgreichem Abschluss aufweisen soll. 

> [!WARNING]
> Wenn Sie `.alter` den Befehl falsch verwenden, kann dies zu Datenverlusten führen.
> Lesen Sie sorgfältig die unter `.alter` schiede `.alter-merge` zwischen und darunter.

`.alter-merge`:

 * Spalten, die nicht vorhanden sind und die Sie angeben, werden am Ende des vorhandenen Schemas hinzugefügt.
 * Wenn das über gegebene Schema nicht einige Tabellen Spalten enthält, werden diese nicht gelöscht.
 * Wenn Sie eine vorhandene Spalte mit einem anderen Typ angegeben haben, schlägt der Befehl fehl.

`.alter`nur (nicht `.alter-merge`):

 * Die Tabelle weist genau die gleichen Spalten in derselben Reihenfolge wie angegeben auf.
 * Vorhandene Spalten, die nicht im Befehl angegeben sind, werden gelöscht (wie in `.drop column`), und die Daten gehen verloren.
 * Das Ändern eines Spalten Typs wird beim Ändern einer Tabelle nicht unterstützt. Verwenden Sie stattdessen den Befehl [. Alter Column](alter-column.md) .

> [!TIP] 
> Verwenden `.show table [TableName] cslschema` Sie, um das vorhandene Spalten Schema zu erhalten, bevor Sie es ändern. 

Folgendes gilt für beide Befehle:

1. Vorhandene Daten werden von diesen Befehlen nicht physisch geändert. Daten in entfernten Spalten werden ignoriert. Es wird davon ausgegangen, dass Daten in neuen Spalten NULL sind.
1. Abhängig von der Konfiguration des Clusters kann die Datenerfassung das Spalten Schema der Tabelle ändern, auch ohne Benutzerinteraktion. Wenn Sie Änderungen am Spalten Schema einer Tabelle vornehmen, sollten Sie daher sicherstellen, dass bei der Erfassung erforderliche Spalten nicht hinzugefügt werden, die der Befehl dann entfernt.

> [!WARNING]
> Daten Erfassungs Prozesse in die Tabelle, die das Spalten Schema der Tabelle ändern, die parallel mit dem `.alter table` Befehl auftreten, können für die Reihenfolge der Tabellen Spalten agnostisch ausgeführt werden. Außerdem besteht das Risiko, dass Daten in die falschen Spalten aufgenommen werden. Verhindern Sie dies, indem Sie die Erfassung während solcher Befehle Beenden oder sicherstellen, dass bei solchen Erfassungs Vorgängen immer ein Mapping-Objekt verwendet wird.

**Beispiele**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```