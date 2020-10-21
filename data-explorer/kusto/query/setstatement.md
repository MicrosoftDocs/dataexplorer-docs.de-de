---
title: 'Set-Anweisung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Set-Anweisung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b8b30aafd8fafc2a900fe0596243a0d4ed89f276
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252068"
---
# <a name="set-statement"></a>set-Anweisung

::: zone pivot="azuredataexplorer"

Die- `set` Anweisung wird verwendet, um eine Abfrage Option für die Dauer der Abfrage festzulegen.
Mit Abfrageoptionen wird gesteuert, wie eine Abfrage ausgeführt wird und wie Ergebnisse zurückgegeben werden. Dabei kann es sich um boolesche Flags (standardmäßig deaktiviert) oder um einen ganzzahligen Wert handeln. Eine Abfrage kann keine, eine oder mehrere set-Anweisungen enthalten. Set-Anweisungen wirken sich nur auf die Anweisungen für tabellarische Ausdrücke aus, die Sie in der Programm Reihenfolge

* Abfrage Optionen können auch Programm gesteuert aktiviert werden, indem Sie im-Objekt festgelegt werden `ClientRequestProperties` . Klicken Sie [hier](../api/netfx/request-properties.md).
  
* Abfrage Optionen sind nicht formal Teil der Kusto-Sprache und können geändert werden, ohne dass Sie als unterbrechende sprach Änderung angesehen werden.

## <a name="syntax"></a>Syntax

`set`*OptionName* [ `=` *OptionValue*]

## <a name="example"></a>Beispiel

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
