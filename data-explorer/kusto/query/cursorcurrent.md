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
ms.openlocfilehash: d9a9526fd846523510e8555c04ff3345d9008348
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030462"
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

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
