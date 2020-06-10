---
title: . ALTER TABLE-Azure Daten-Explorer
description: In diesem Artikel wird der. ALTER TABLE-Befehl beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: 29f0c65a635b6e4fe6ffe3288cc1dcdde702fc8a
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84665026"
---
# <a name="alter-table"></a>.alter table
 
Mit dem Befehl `.alter table`:
* Sichert Daten in "beibehaltenen" Spalten
* Neuanordnung von Tabellen Spalten
* Legt ein neues Spalten Schema, `docstring` den Ordner und auf eine vorhandene Tabelle fest und überschreibt das vorhandene Spalten Schema, den `docstring` Ordner und.
* Muss im Kontext einer bestimmten Datenbank ausgeführt werden, in der der Tabellenname festgelegt ist.
* Erfordert die [Berechtigung "Table admin](../management/access-control/role-based-authorization.md) "

> [!WARNING]
> Wenn Sie den `.alter` Befehl falsch verwenden, kann dies zu Datenverlusten führen.

> [!TIP]
> Der `.alter` verfügt über eine Entsprechung, den `.alter-merge` Tabellen Befehl, der über eine ähnliche Funktionalität verfügt. Weitere Informationen finden Sie unter [. Alter-Merge Table](../management/alter-merge-table-command.md)

**Syntax**

`.alter``table` *TableName* (*ColumnName*:*ColumnType*,...)  [ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*] `)` ]


 * Die Tabelle weist genau die gleichen Spalten in derselben Reihenfolge wie angegeben auf.
 Tabellen Spalten angeben:
 * Wenn im Befehl keine vorhandenen Spalten angegeben werden, werden Sie gelöscht, und die darin vorhandenen Daten gehen verloren, wie bei dem `.drop column` Befehl.
 * Wenn Sie eine Tabelle ändern, wird das Ändern eines Spalten Typs nicht unterstützt. Verwenden Sie stattdessen den Befehl [. Alter Column](alter-column.md) .

> [!TIP]
> Verwenden Sie `.show table [TableName] cslschema` , um das vorhandene Spalten Schema zu erhalten, bevor Sie es ändern.


Wie wirkt sich der Befehl auf die Daten aus?
* Vorhandene Daten werden vom Befehl nicht physisch geändert. Daten in entfernten Spalten werden ignoriert. Es wird davon ausgegangen, dass Daten in neuen Spalten NULL sind.
* Abhängig von der Konfiguration des Clusters kann die Datenerfassung das Spalten Schema der Tabelle ändern, auch ohne Benutzerinteraktion. Wenn Sie Änderungen am Spalten Schema einer Tabelle vornehmen, müssen Sie sicherstellen, dass die Erfassung erforderliche Spalten nicht hinzufügt, die der Befehl dann entfernt.

> [!WARNING]
> Daten Erfassungs Prozesse in der Tabelle, die das Spalten Schema der Tabelle ändern und parallel mit dem `.alter table` Befehl auftreten, werden möglicherweise für die Reihenfolge der Tabellen Spalten agnostisch ausgeführt. Außerdem besteht das Risiko, dass Daten in die falschen Spalten aufgenommen werden. Verhindern Sie diese Probleme, indem Sie die Erfassung während des Befehls beenden oder sicherstellen, dass bei solchen Erfassungs Vorgängen immer ein Mapping-Objekt verwendet wird.

**Beispiele**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
