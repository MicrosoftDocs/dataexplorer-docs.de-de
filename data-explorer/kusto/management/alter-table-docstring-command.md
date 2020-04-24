---
title: . ALTER TABLE DocString-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird. ALTER TABLE DocString in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5b3fa21b8b9012fe23a82afea77b1a3e24ae3c91
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108472"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Ändert den DocString-Wert einer vorhandenen Tabelle.

`.alter``table` *Dokumentation* zu *TableName* `docstring`

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Eine Änderung der Tabelle ist auch für den [Datenbankbenutzer](../management/access-control/role-based-authorization.md) zulässig, der die Tabelle ursprünglich erstellt hat.
> * Wenn die Tabelle nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Tabelle finden Sie unter [. Create Table.](create-table-command.md)

**Beispiel** 

```
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```