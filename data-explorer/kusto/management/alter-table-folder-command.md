---
title: . ALTER TABLE Folder-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird der ALTER TABLE-Ordner in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: f03a2927d3f0a4fe7ee1719594f2d1f19e231d0f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618359"
---
# <a name="alter-table-folder"></a>.alter table folder

Ändert den Ordner Wert einer vorhandenen Tabelle. 

`.alter``table` *Ordner* " *TableName* `folder` "

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Tabelle ursprünglich erstellt hat, kann Sie auch bearbeiten.
> * Wenn die Tabelle nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Tabelle finden Sie unter [. Create Table.](create-table-command.md)

**Beispiele** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```