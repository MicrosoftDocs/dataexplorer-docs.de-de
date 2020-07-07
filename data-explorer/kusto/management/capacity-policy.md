---
title: 'Befehle zur Kapazitäts Richtlinien Steuerung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden die Befehle zur Kapazitäts Richtlinien Steuerung in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 512ab14c2abc1f777376d81d4944caf2c3343513
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967331"
---
# <a name="capacity-policy-commands"></a>Kapazitäts Richtlinien Befehle

Die folgenden Steuerelement Befehle können zum Verwalten der [Kapazitäts Richtlinie](../management/capacitypolicy.md)eines Clusters verwendet werden.

Die Befehle erfordern [alldatabasesadmin](../management/access-control/role-based-authorization.md) -Berechtigungen.

## <a name="show-cluster-policy-capacity"></a>Cluster Richtlinien Kapazität anzeigen

```kusto
.show cluster policy capacity
```

Zeigt die aktuelle Kapazitäts Richtlinie für den Cluster an.

**Ausgabe**

|Richtlinienname | Entitätsname | Policy | Untergeordnete Entitäten | Entitätstyp
|---|---|---|---|---
|Capacitypolicy | | JSON-formatierte Zeichenfolge, die die Richtlinie darstellt | Die Liste der Datenbanken im Cluster. |Cluster


## <a name="alter-cluster-policy-capacity"></a>Ändern der Kapazität der Cluster Richtlinie

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**Hinweis**: Es kann bis zu einer Stunde dauern, bis Änderungen an der Cluster Kapazitäts Richtlinie wirksam werden.

**Beispiele:**

* Explizites ändern aller Eigenschaften der Cluster Richtlinie:

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

* Ändern einer einzelnen Eigenschaft der Richtlinie auf Cluster Ebene, wobei alle anderen Eigenschaften intakt bleiben:

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
