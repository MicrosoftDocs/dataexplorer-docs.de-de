---
title: cursor_current (), current_cursor ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird cursor_current (), current_cursor () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1b2e6f868d5117cbdd3db6ba88c6b77e5f0e8ccf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252445"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

Ruft den aktuellen Wert des Cursors der Datenbank im Gültigkeitsbereich ab. (Die Namen `cursor_current` und `current_cursor` sind Synonyme.)

## <a name="syntax"></a>Syntax

`cursor_current()`

## <a name="returns"></a>Rückgabe

Gibt einen einzelnen Wert des Typs zurück `string` , der den aktuellen Wert des Cursors der Datenbank im Gültigkeitsbereich codiert.

**Notizen**

Weitere Informationen zu Datenbankcursorn finden Sie unter [Daten Bank Cursor](../management/databasecursor.md) .

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
