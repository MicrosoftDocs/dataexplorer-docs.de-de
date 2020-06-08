---
title: . ALTER TABLE DocString-Azure Daten-Explorer
description: Dieser Artikel beschreibt den `.alter table docstring` Befehl in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 65b71ab15763506683c461f04975d22d396f6405
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512536"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Ändert den DocString-Wert einer vorhandenen Tabelle.

`.alter``table`Dokumentation zu *TableName* `docstring` *Documentation*

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Tabelle ursprünglich erstellt hat, darf ihn ändern.
> * Wenn die Tabelle nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Tabelle finden Sie unter [. Create Table](create-table-command.md)

**Beispiel** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
