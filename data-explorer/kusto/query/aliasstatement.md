---
title: Alias-Anweisung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Alias-Anweisung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c6c689ab6daacebe1cd20742b199c8b9cc299245
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766095"
---
# <a name="alias-statement"></a>alias-Anweisung

::: zone pivot="azuredataexplorer"

Alias-Anweisungen ermöglichen das Definieren von Alias für Datenbanken, die später in derselben Abfrage verwendet werden können.

Dies ist nützlich, wenn Sie mit mehreren Clustern arbeiten, während Sie versuchen, als Arbeit auf weniger Clustern oder nur auf einem Cluster angezeigt zu werden.
Der Alias muss gemäß der folgenden Syntax definiert werden, wobei *Clustername* und *Datenbankname* eine vorhandene und gültige Entität sein müssen.

**Syntax**

`alias`database[*'DatabaseAliasName'*] `=` cluster("https://*clustername*.kusto.windows.net:443").database("*databasename*")

`alias`Datenbank *DatabaseAliasName* `=` cluster("https://*clustername*.kusto.windows.net:443").database("*databasename*")

* *'DatabaseAliasName'* kann entweder ein Achsenname oder ein neuer Name sein.
* Der zugeordnete Cluster-uri und der zugeordnete Datenbankname müssen in Doppelanführungszeichen(") oder Einzelquotes(') angezeigt werden.

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

Dies wird in Azure Monitor nicht unterstützt.

::: zone-end
