---
title: current_cluster_endpoint() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird current_cluster_endpoint() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 65ce130b4dd3e0a3125eefc6c410775647f9b964
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516846"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

Gibt den Netzwerkendpunkt (DNS-Name) des aktuellen Abgefragten zurück.

**Syntax**

`current_cluster_endpoint()`

**Rückgabe**

Der Netzwerkendpunkt (DNS-Name) des aktuellen Clusters, der `string`abgefragt wird, als Wert des Typs .

**Beispiel**

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```