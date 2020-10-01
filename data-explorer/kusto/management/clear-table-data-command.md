---
title: '. Löschen von Tabellendaten: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den `.clear table data` Befehl in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vrozov
ms.service: data-explorer
ms.topic: reference
ms.date: 10/01/2020
ms.openlocfilehash: 513544b8fb373f7242bd36b250790801b39061b2
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615072"
---
# <a name="clear-table-data"></a>. Löschen von Tabellendaten

Löscht die Daten einer vorhandenen Tabelle, einschließlich der Daten zur Erfassung von Datenströmen.

`.clear``table` *TableName*`data` 

> [!NOTE]
> * Erfordert die [Berechtigung "Table admin](../management/access-control/role-based-authorization.md) "

**Beispiel** 

```kusto
.clear table LyricsAsTable data 
```
 
