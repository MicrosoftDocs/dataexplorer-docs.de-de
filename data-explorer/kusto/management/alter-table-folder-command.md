---
title: .alter-Tabellenordner - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den Tabellenordner .alter in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: a1d368d50f0e42acdbc25348b8025fbe8b530536
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522303"
---
# <a name="alter-table-folder"></a>.alter table folder

Ändert den Ordnerwert einer vorhandenen Tabelle. 

`.alter``table` *TableName-Ordner* `folder` *Folder*

> [!NOTE]
> * Erfordert [Datenbankadministratorberechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer,](../management/access-control/role-based-authorization.md) der die Tabelle ursprünglich erstellt hat, darf sie auch bearbeiten.
> * Wenn die Tabelle nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Tabelle finden Sie unter [.create-Tabelle](create-table-command.md)

**Beispiele** 

```
.alter table MyTable folder "Updated folder"
```

```
.alter table MyTable folder @"First Level\Second Level"
```