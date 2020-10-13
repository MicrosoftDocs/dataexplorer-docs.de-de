---
title: 'Sandbox-Richtlinie: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Erfahren Sie mehr über Sandbox-Richtlinien Befehle in Azure Daten-Explorer. Weitere Informationen finden Sie unter Vorgehensweise beim Anzeigen, anpassen und Ablegen von Sandbox Richtlinien.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ba37147db87c436aff7d77790641b17576e86392
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002963"
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
