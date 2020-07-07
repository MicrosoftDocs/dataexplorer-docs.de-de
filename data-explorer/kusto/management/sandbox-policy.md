---
title: 'Sandbox-Richtlinie: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Sandbox Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7d56d602f53db29f5ea558acb0e9e4288e5ac6e3
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967484"
---
# <a name="sandbox-policy-command"></a>Befehl "Sandbox Richtlinie"

Die folgenden Befehle ermöglichen die Verwaltung von [Sand Fächern](../concepts/sandboxes.md) und [Sandbox-Richtlinien](sandboxpolicy.md) in einem Kusto-Engine-Dienst.

Die Befehle erfordern [alldatabasesadmin](access-control/role-based-authorization.md) -Berechtigungen.

## <a name="sandbox-policy"></a>Sandbox-Richtlinie

### <a name="show-cluster-policy-sandbox"></a>. Cluster Richtlinien-Sandkasten anzeigen

Zeigt alle konfigurierten Sandbox-Richtlinien auf Cluster Ebene an.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>. Alter Cluster Policy Sandbox

Ändert die Auflistung von Sandbox-Richtlinien auf Cluster Ebene.

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

### <a name="drop-cluster-policy-sandbox"></a>. Drop Cluster Policy Sandbox

Verwenden Sie den folgenden Befehl, um **alle** Sandbox-Richtlinien zu löschen:

```kusto
.delete cluster policy sandbox
```

