---
title: current_principal ()-Azure Daten-Explorer
description: In diesem Artikel wird current_principal () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b1d45fb8b0749a4be30854dd9b0120a7eb127bf2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227297"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Gibt den aktuellen Prinzipal Namen zurück, der die Abfrage ausgeführt hat.

**Syntax**

`current_principal()`

**Rückgabe**

Der aktuelle Prinzipal voll qualifizierte Name (fqn) als `string` .  
Die Zeichenfolge ist wie folgt formatiert:  
*Principlatype* `=` *Principalid* `;` *Tenantid*

**Beispiel**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print fqn=current_principal()
```

|fqn|
|---|
|aaduser = 346e950 e-4a62-42bf-96f5-4cf4eac3f11e; 72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
