---
title: 'Alias-Anweisung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Alias-Anweisung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 63c639fb95322c537c5e069aa7e8ef7037371c88
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742023"
---
# <a name="alias-statement"></a>alias-Anweisung

::: zone pivot="azuredataexplorer"

Mit Alias Anweisungen können Sie einen Alias für Datenbanken definieren, der später in derselben Abfrage verwendet werden kann.

Dies ist nützlich, wenn Sie mit mehreren Clustern arbeiten, aber so aussehen möchten, als ob Sie an weniger Clustern arbeiten.
Der Alias muss entsprechend der folgenden Syntax definiert werden, wobei *Cluster Name* und *DatabaseName* vorhandene und gültige Entitäten sind.

**Syntax**

`alias`Database [*' databasealiasname '*] `=` Cluster ("https://*Cluster Name*. Kusto. Windows. net: 443"). Database ("*DatabaseName*")

`alias`Database *databasealiasname* `=` Cluster ("https://*Cluster Name*. Kusto. Windows. net: 443"). Database ("*DatabaseName*")

* *"Databasealiasname"* kann entweder ein vorhandener oder neuer Name sein.
* Der zugeordnete Cluster-URI und der zugeordnete Datenbankname müssen in doppelten Anführungszeichen (") oder in einfachen Anführungszeichen (') enthalten sein.

**Beispiele**

```kusto
alias database["wiki"] = cluster("https://somecluster.kusto.windows.net:443").database("somedatabase");
database("wiki").PageViews | count 
```

```kusto
alias database Logs = cluster("https://othercluster.kusto.windows.net:443").database("otherdatabase");
database("Logs").Traces | count 
```

::: zone-end

::: zone pivot="azuremonitor"

Diese Funktion wird in Azure Monitor nicht unterstützt.

::: zone-end
