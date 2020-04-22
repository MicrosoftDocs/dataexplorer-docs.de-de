---
title: current_principal() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird current_principal() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 36cebef7db3042bb59ccc5c7c25a56b2c1a661dc
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766043"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Gibt den aktuellen Prinzipalnamen zurück, der die Abfrage ausführt.

**Syntax**

`current_principal()`

**Rückgabe**

Der aktuelle Haupt-Vollqualifikationsname (FQN) `string`als .  
Die Zeichenfolge wird wie:  
*PrinciplaType*`=`*PrincipalId*`;`*TenantId*

**Beispiel**

```kusto
print fqn=current_principal()
```

|fqn|
|---|
|aaduser=346e950e-4a62-42bf-96f5-4cf4eac3f11e;72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
