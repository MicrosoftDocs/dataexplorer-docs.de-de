---
title: Sandbox-Richtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt sandbox-Richtlinien in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7ac5a92b2084eaf2b447f296be34b2f4e79e1bb7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520127"
---
# <a name="sandbox-policy"></a>Sandbox-Richtlinie

Die folgenden Befehle ermöglichen die Verwaltung von [Sandboxes](../concepts/sandboxes.md) und [Sandbox-Richtlinien](sandboxpolicy.md) in einem Kusto-Engine-Dienst.

Die Befehle erfordern [AllDatabasesAdmin-Berechtigungen.](access-control/role-based-authorization.md)

## <a name="sandbox-policy"></a>Sandbox-Richtlinie

### <a name="show-cluster-policy-sandbox"></a>.show Cluster-Richtlinien-Sandbox

Zeigt alle konfigurierten Sandkastenrichtlinien auf Clusterebene an.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>.alter Cluster-Richtlinien-Sandbox

Ändert die Auflistung von Sandbox-Richtlinien auf Clusterebene.

```kusto
.alter cluster policy sandbox @'['
  '{'
    '"SandboxKind": "PythonExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '},'
  '{'
    '"SandboxKind": "RExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '}'
']'
```

### <a name="drop-cluster-policy-sandbox"></a>.drop Cluster-Richtlinien-Sandbox

Verwenden Sie den folgenden Befehl, um **alle** Sandbox-Richtlinien zu löschen:

```kusto
.delete cluster policy sandbox
```

