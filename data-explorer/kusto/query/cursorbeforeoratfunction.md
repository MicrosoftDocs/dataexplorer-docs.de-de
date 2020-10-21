---
title: cursor_before_or_at ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird cursor_before_or_at () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8ea3948173270ee8bf6fcca127c9422c5672d621
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245118"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

Ein Prädikat für die Datensätze einer Tabelle, um Ihre Erfassungs Zeit mit einem Daten Bank Cursor zu vergleichen.

## <a name="syntax"></a>Syntax

`cursor_before_or_at``(` *RHS*`)`

## <a name="arguments"></a>Argumente

* *RHS*: entweder ein leeres Zeichenfolgenliteralwert oder ein gültiger Daten Bank Cursor Wert.

## <a name="returns"></a>Rückgabe

Ein Skalarwert des Typs `bool` , der angibt, ob der Datensatz vor oder bei dem *RHS* () des Daten Bank Cursors erfasst wurde () `true` `false` .

**Notizen**

Weitere Informationen zu Datenbankcursorn finden Sie unter [Daten Bank Cursor](../management/databasecursor.md) .

Diese Funktion kann nur für Datensätze einer Tabelle aufgerufen werden, für die die [ingestiontime-Richtlinie](../management/ingestiontimepolicy.md) aktiviert ist.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
