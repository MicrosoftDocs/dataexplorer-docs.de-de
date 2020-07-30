---
title: ingestion_time ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird ingestion_time () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f40a592521082667815fe3ff38843a2376bda0aa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347407"
---
# <a name="ingestion_time"></a>ingestion_time()

::: zone pivot="azuredataexplorer"

Gibt die ungefähre Zeit zurück, zu der der aktuelle Datensatz erfasst wurde.

Diese Funktion muss im Kontext einer Tabelle mit erfassten Daten verwendet werden, für die die [ingestiontime-Richtlinie](../management/ingestiontimepolicy.md) aktiviert war, als die Daten erfasst wurden. Andernfalls erzeugt diese Funktion NULL-Werte.

::: zone-end

::: zone pivot="azuremonitor"

Ruft den ab, `datetime` Wenn der Datensatz erfasst wurde und für Abfragen bereit ist.

::: zone-end

> [!NOTE]
> Der von dieser Funktion zurückgegebene Wert ist nur annähernd, da der Erfassungs Vorgang einige Minuten in Anspruch nehmen kann, und mehrere Erfassungs Aktivitäten können gleichzeitig stattfinden. Verwenden Sie [Datenbankcursorn](../management/databasecursor.md), um alle Datensätze einer Tabelle mit genau einer Garantie zu verarbeiten.

## <a name="syntax"></a>Syntax

`ingestion_time()`

## <a name="returns"></a>Rückgabe

Ein- `datetime` Wert, der die ungefähre Zeit der Erfassung in eine Tabelle angibt.

## <a name="example"></a>Beispiel

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
