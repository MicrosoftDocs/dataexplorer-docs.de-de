---
title: Kapazitätsrichtliniensteuerungsbefehle - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Die Verwaltungsbefehle für Kapazitätsrichtlinien in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 929cfa885a7373b400b832d908677a7a5fb93ef6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522082"
---
# <a name="capacity-policy-control-commands"></a>Kapazitätsrichtliniensteuerungsbefehle

Die folgenden Steuerbefehle können zum Verwalten der [Kapazitätsrichtlinie](../management/capacitypolicy.md)eines Clusters verwendet werden.

Die Befehle erfordern [AllDatabasesAdmin-Berechtigungen.](../management/access-control/role-based-authorization.md)

## <a name="show-cluster-policy-capacity"></a>Clusterrichtlinienkapazität anzeigen

```kusto
.show cluster policy capacity
```

Zeigt die aktuelle Kapazitätsrichtlinie für den Cluster an.

**Ausgabe**

|Richtlinienname | Entitätsname | Richtlinie | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|CapacityPolicy | | JSON-formatierte Zeichenfolge, die die Richtlinie darstellt | Die Liste der Datenbanken im Cluster |Cluster


## <a name="alter-cluster-policy-capacity"></a>Ändern der Clusterrichtlinienkapazität

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**Hinweis:** Änderungen an der Clusterkapazitätsrichtlinie können bis zu 1 Stunde dauern.

**Beispiele:**

* Explizites Ändern aller Eigenschaften der Clusterrichtlinie:

```kusto
.alter cluster policy capacity
'{'
  '"IngestionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 512,'
    '"CoreUtilizationCoefficient": 0.75'
  '},'
  '"ExtentsMergeCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExtentsPurgeRebuildCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExportCapacity": {'
    '"ClusterMaximumConcurrentOperations": 100,'
    '"CoreUtilizationCoefficient": 0.25'
  '},'
  '"ExtentsPartitionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 4'
  '}'
'}'
```

* Ändern einer einzelnen Eigenschaft der Clusterebene, wobei alle anderen Eigenschaften erhalten bleiben:

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
