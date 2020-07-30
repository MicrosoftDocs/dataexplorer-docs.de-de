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
ms.openlocfilehash: 879fe4aac2a6714f3d7ab16a63c69cd8215bbb04
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348597"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

Ruft den aktuellen Wert des Cursors der Datenbank im Gültigkeitsbereich ab. (Die Namen `cursor_current` und `current_cursor` sind Synonyme.)

## <a name="syntax"></a>Syntax

`cursor_current()`

## <a name="returns"></a>Rückgabe

Gibt einen einzelnen Wert des Typs zurück `string` , der den aktuellen Wert des Cursors der Datenbank im Gültigkeitsbereich codiert.

**Hinweise**

Weitere Informationen zu Datenbankcursorn finden Sie unter [Daten Bank Cursor](../management/databasecursor.md) .

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
