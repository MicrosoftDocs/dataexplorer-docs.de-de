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
ms.openlocfilehash: 0a59e25c6c38d3189330299af1b19f89357cb456
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520059"
---
# <a name="sandbox-policy"></a>Sandbox-Richtlinie

## <a name="overview"></a>Übersicht

Kusto unterstützt das Ausführen bestimmter Plugins in [Sandboxes](../concepts/sandboxes.md), in denen die für die Sandbox verfügbaren Ressourcen begrenzt und kontrolliert sind, sowohl für Sicherheitszwecke als auch für Ressourcen-Governance-Zwecke.

Sandboxen werden auf den Knoten des Kusto-Engine-Dienstes ausgeführt, und einige ihrer Einschränkungen werden in Sandbox-Richtlinien definiert, wobei jede Sandbox-Art über eine eigene Sandbox-Richtlinie verfügen kann.

Sandbox-Richtlinien werden auf Clusterebene verwaltet und wirken sich auf alle Knoten im Cluster aus.

Das Ändern der Richtlinien erfordert [AllDatabasesAdmin-Berechtigungen.](../management/access-control/role-based-authorization.md)

## <a name="the-policy-object"></a>Das Richtlinienobjekt

Eine Sandbox-Richtlinie weist die folgenden Eigenschaften auf:

* **SandboxKind**: definiert die Art der Sandbox `PythonExecution`(z.B. , `RExecution`).
* **IsEnabled**: Definiert, ob Sandboxes dieser Art für die Ausführung auf den Knoten des Clusters aktiviert sind.
* **TargetCountPerNode**: Definiert, wie viele Sandboxes dieser Art auf den Knoten des Clusters ausgeführt werden dürfen.
  * Die Werte können zwischen 1 und dem Doppelten der Anzahl der Prozessoren pro Knoten liegen.
  * Standardwert: `16`.
* **MaxCpuRatePerSandbox**: definiert die maximale CPU-Rate in Prozent aller verfügbaren Kerne, die eine einzelne Sandbox verwenden kann.
  * Die Werte können zwischen 1 und 100 liegen.
  * Standardwert: `50`.
* **MaxMemoryMbPerSandbox**: definiert die maximale Speichermenge (in Megabyte), die eine einzelne Sandbox verwenden kann.
  * Die Werte können zwischen 200 und 65536 (64 GB) liegen.
  * Der Standardwert `20480` ist (20 GB).

## <a name="example"></a>Beispiel

Die folgende Richtlinie legt unterschiedliche Grenzwerte für 2 `PythonExecution` `RExecution`verschiedene Arten von Sandboxen fest - und:

```json
[
  {
    "SandboxKind": "PythonExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 4,
    "MaxCpuRatePerSandbox": 55,
    "MaxMemoryMbPerSandbox": 65536
  },
  {
    "SandboxKind": "RExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 2,
    "MaxCpuRatePerSandbox": 50,
    "MaxMemoryMbPerSandbox": 10240
  }
]
```

## <a name="notes"></a>Hinweise

* Änderungen an der Sandbox-Richtlinie gelten für Sanboxen, die ab dem Zeitpunkt der Anwendung der Änderung erstellt werden.
  * Sandboxes, die vor der Richtlinienänderung vorab zugewiesen wurden, werden weiterhin gemäß den vorherigen Richtlinienlimits ausgeführt, bis sie als Teil einer Abfrage verwendet werden.
* Es kann zu einer Verzögerung von bis zu 5 Minuten kommen, bis die Änderung der Richtlinie wirksam wird, da die Clusterknoten regelmäßig nach Richtlinienänderungen suchen.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Sandkastenrichtliniensteuerungsbefehle,](../management/sandbox-policy.md) um die Sandkastenrichtlinie des Clusters zu verwalten.