---
title: cursor_current (), current_cursor ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird cursor_current (), current_cursor () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5291fa664dc4736179d7f20984eacfd44efd5888
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737656"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

Ruft den aktuellen Wert des Cursors der Datenbank im Gültigkeitsbereich ab. (Die Namen `cursor_current` und `current_cursor` sind Synonyme.)

**Syntax**

`cursor_current()`

**Rückgabe**

Gibt einen einzelnen Wert des Typs `string` zurück, der den aktuellen Wert des Cursors der Datenbank im Gültigkeitsbereich codiert.

**Hinweise**

Weitere Informationen zu Datenbankcursorn finden Sie unter [Daten Bank Cursor](../management/databasecursor.md) .

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
