---
title: current_principal ()-Azure Daten-Explorer
description: In diesem Artikel wird current_principal () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a2530b93bd5102b7c21aa535eb8e7ca7c4360ddf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252498"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

Gibt den aktuellen Prinzipal Namen zurück, der die Abfrage ausführt.

## <a name="syntax"></a>Syntax

`current_principal()`

## <a name="returns"></a>Rückgabe

Der aktuelle voll qualifizierte Prinzipal Name (fqn) als `string` .  
Das Zeichen folgen Format lautet wie folgt:  
*Principlatype* `=` *Principalid* `;` *Tenantid*

## <a name="example"></a>Beispiel

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
