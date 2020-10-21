---
title: cursor_after ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird cursor_after () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3b1a89ab84b62241058a24573c0362f7215f82c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252476"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

Ein Prädikat für die Datensätze einer Tabelle, um Ihre Erfassungs Zeit mit einem Daten Bank Cursor zu vergleichen.

## <a name="syntax"></a>Syntax

`cursor_after``(` *RHS*`)`

## <a name="arguments"></a>Argumente

* *RHS*: entweder ein leeres Zeichenfolgenliteralwert oder ein gültiger Daten Bank Cursor Wert.

## <a name="returns"></a>Rückgabe

Ein Skalarwert des Typs `bool` , der angibt, ob der Datensatz nach dem *RHS* () des Daten Bank Cursors erfasst wurde `true` () `false` .

**Notizen**

Weitere Informationen zu Datenbankcursorn finden Sie unter [Daten Bank Cursor](../management/databasecursor.md) .

Diese Funktion kann nur für Datensätze einer Tabelle aufgerufen werden, für die die [ingestiontime-Richtlinie](../management/ingestiontimepolicy.md) aktiviert ist.

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
