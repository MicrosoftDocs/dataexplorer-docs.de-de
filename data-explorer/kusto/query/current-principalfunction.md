---
title: current_principal ()-Azure Daten-Explorer | Microsoft-Dokumentation
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
ms.openlocfilehash: 0561ac200105015e6d1c1cce1c16fe5f60fc2ccf
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737707"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Gibt den aktuellen Prinzipal Namen zurück, der die Abfrage ausgeführt hat.

**Syntax**

`current_principal()`

**Rückgabe**

Der aktuelle Prinzipal voll qualifizierte Name (fqn) als `string`.  
Die Zeichenfolge ist wie folgt formatiert:  
*Principlatype*`=`*principalid*`;`*tenantid*

**Beispiel**

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
