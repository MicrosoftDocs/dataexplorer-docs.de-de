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
ms.openlocfilehash: fc449cb6a376eba66457855173c6e992e6ca1dbc
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321657"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Ändert den DocString-Wert einer vorhandenen Tabelle.

`.alter``table`Dokumentation zu *TableName* `docstring` *Documentation*

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Tabelle ursprünglich erstellt hat, darf ihn ändern.
> * Wenn die Tabelle nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Tabelle finden Sie unter. [`.create table`](create-table-command.md)

**Beispiel** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
