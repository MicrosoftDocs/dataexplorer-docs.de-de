---
title: cursor_before_or_at() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden cursor_before_or_at() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4d1752c69a6f3515b94c4050cef8f518ff58a235
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765967"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

Ein Prädikat über die Datensätze einer Tabelle, um deren Aufnahmezeit mit einem Datenbankcursor zu vergleichen.

**Syntax**

`cursor_before_or_at``(` *RHS*`)`

**Argumente**

* *RHS*: Entweder ein leeres Zeichenfolgenliteral oder ein gültiger Datenbankcursorwert.

**Rückgabe**

Ein skalarer Wert `bool` des Typs, der angibt, ob der Datensatz`true`vor oder`false`am Datenbankcursor *RHS* ( ) aufgenommen wurde oder nicht ( ).

**Hinweise**

Weitere Informationen zu Datenbankcursorn finden Sie unter [Datenbankcursor.](../management/databasecursor.md)

Diese Funktion kann nur für Datensätze einer Tabelle aufgerufen werden, für die die [IngestionTime-Richtlinie](../management/ingestiontimepolicy.md) aktiviert ist.

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
