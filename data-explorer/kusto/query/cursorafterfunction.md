---
title: cursor_after ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird cursor_after () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 9fab1ec936e950368667fc3afb133dcd952e44b5
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737690"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

Ein Prädikat für die Datensätze einer Tabelle, um Ihre Erfassungs Zeit mit einem Daten Bank Cursor zu vergleichen.

**Syntax**

`cursor_after``(` *RHS*`)`

**Argumente**

* *RHS*: entweder ein leeres Zeichenfolgenliteralwert oder ein gültiger Daten Bank Cursor Wert.

**Rückgabe**

Ein Skalarwert des Typs `bool` , der angibt, ob der Datensatz nach dem *RHS* (`true`) des Daten Bank Cursors erfasst wurde`false`().

**Hinweise**

Weitere Informationen zu Datenbankcursorn finden Sie unter [Daten Bank Cursor](../management/databasecursor.md) .

Diese Funktion kann nur für Datensätze einer Tabelle aufgerufen werden, für die die [ingestiontime-Richtlinie](../management/ingestiontimepolicy.md) aktiviert ist.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
