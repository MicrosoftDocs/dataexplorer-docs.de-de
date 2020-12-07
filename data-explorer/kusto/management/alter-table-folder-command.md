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
ms.openlocfilehash: 8ba9eb06e07dd513d20cdade87322d9ef5f17d24
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321640"
---
# <a name="alter-table-folder"></a>.alter table folder

Ändert den Ordner Wert einer vorhandenen Tabelle. 

`.alter``table`Ordner " *TableName* `folder` *Folder* "

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Tabelle ursprünglich erstellt hat, kann Sie auch bearbeiten.
> * Wenn die Tabelle nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Tabelle finden Sie unter. [`.create table`](create-table-command.md)

**Beispiele** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```