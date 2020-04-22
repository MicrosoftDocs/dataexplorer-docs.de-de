---
title: Set-Anweisung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Set-Anweisung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8cb9c1d72f1b2e8e4bfbbd28d67c04295c9ccf5b
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765571"
---
# <a name="set-statement"></a>set-Anweisung

::: zone pivot="azuredataexplorer"

Die `set` Anweisung wird verwendet, um eine Abfrageoption für die Dauer der Abfrage festzulegen.
Mit Abfrageoptionen wird gesteuert, wie eine Abfrage ausgeführt wird und wie Ergebnisse zurückgegeben werden. Sie können boolesche Flags sein (standardmäßig deaktiviert) oder einen Ganzzahlwert haben. Eine Abfrage kann keine, eine oder mehrere set-Anweisungen enthalten. Set-Anweisungen wirken sich nur auf die tabellenförmigen Ausdrucksanweisungen aus, die sie in der Programmreihenfolge nachfolgen.

* Abfrageoptionen können auch programmgesteuert aktiviert werden, `ClientRequestProperties` indem sie im Objekt gesetzt werden. Klicken Sie [hier](../api/netfx/request-properties.md).
  
* Abfrageoptionen sind formal kein Teil der Kusto-Sprache und können geändert werden, ohne als eine brechende Sprachänderung betrachtet zu werden.

**Syntax**

`set`*OptionName* `=` [ *OptionValue*]

**Beispiel**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
