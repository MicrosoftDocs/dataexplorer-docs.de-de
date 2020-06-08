---
title: 'Sandbox-Richtlinie: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Sandbox Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 786f771878a7216b62dce127391f0e7967e954f6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512452"
---
# <a name="sandbox-policy"></a>Sandbox-Richtlinie

Azure Daten-Explorer führt bestimmte Plug-ins in [Sandkästen](../concepts/sandboxes.md) aus, deren verfügbare Ressourcen auf Sicherheit und Ressourcenkontrolle beschränkt sind.

Sand Fächer werden auf den Knoten der Kusto-Engine ausgeführt. Einige ihrer Einschränkungen sind in Sandbox-Richtlinien definiert, in denen jeder Sandbox-Typ seine eigene Richtlinie aufweisen kann.

Sandbox-Richtlinien werden auf Cluster Ebene verwaltet und wirken sich auf alle Knoten im Cluster aus.

Um die Richtlinien zu ändern, benötigen Sie [alldatabasesadmin](../management/access-control/role-based-authorization.md) -Berechtigungen.

## <a name="the-policy-object"></a>Das Policy-Objekt

Eine Sandbox-Richtlinie verfügt über die folgenden Eigenschaften:

* **Sandboxkind**: definiert den Typ des Sandkastens (z `PythonExecution` . b.,, `RExecution` ).
* **Isaktivierte**: definiert, ob Sandkästen dieses Typs auf den Knoten des Clusters ausgeführt werden können.
* **Targetzähl-pernode**: definiert, wie viele Sand Fächer dieses Typs auf den Knoten des Clusters ausgeführt werden dürfen.
  * Die Werte können zwischen 1 und doppelt so groß wie die Anzahl der Prozessoren pro Knoten liegen.
  * Der Standardwert ist 16.
* **Maxcpuratepersandbox**: definiert die maximale CPU-Rate als Prozentsatz aller verfügbaren Kerne, die von einem einzelnen Sandkasten genutzt werden können.
  * Die Werte können zwischen 1 und 100 liegen.
  * Der Standardwert lautet "50".
* **Maxmemorymbpersandbox**: definiert den maximalen Arbeitsspeicher (in Megabyte), der von einem einzelnen Sandkasten verwendet werden kann.
  * Die Werte können zwischen 200 und 65536 (64 GB) liegen.
  * Der Standardwert ist 20480 (20 GB).

## <a name="example"></a>Beispiel

Die folgende Richtlinie legt verschiedene Grenzwerte für `PythonExecution` -und- `RExecution` Sandkästen fest:

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

> [!NOTE]
> * Änderungen an der Sandbox Richtlinie gelten für Sandboxes, die ab dem Zeitpunkt der Änderung der Änderung erstellt werden. Sand Fächer, die vor der Richtlinien Änderung vorab zugeordnet wurden, werden gemäß den oben genannten Richtlinien Limits weiter ausgeführt, bis Sie als Teil einer Abfrage verwendet werden.
> * Es kann bis zu fünf Minuten dauern, bis die Änderung der Richtlinie wirksam wird, da die Cluster Knoten regelmäßig nach Richtlinien Änderungen Abfragen.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Befehle der Sandbox-Richtlinien Steuerung](../management/sandbox-policy.md) , um die Sandbox Richtlinie des Clusters zu verwalten.
 
