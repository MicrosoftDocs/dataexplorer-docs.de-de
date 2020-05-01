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
ms.openlocfilehash: 790cd9805be6dd4440ef2eb51c504044dc069b3c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617781"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Ändert den DocString-Wert einer vorhandenen Tabelle.

`.alter``table` *Dokumentation* zu *TableName* `docstring`

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Eine Änderung der Tabelle ist auch für den [Datenbankbenutzer](../management/access-control/role-based-authorization.md) zulässig, der die Tabelle ursprünglich erstellt hat.
> * Wenn die Tabelle nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Tabelle finden Sie unter [. Create Table.](create-table-command.md)

**Beispiel** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```