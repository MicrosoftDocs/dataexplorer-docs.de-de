---
title: current_cluster_endpoint ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird current_cluster_endpoint () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2c3ddbee55e729ae8afbb6c1fbcc213bd6bfd9ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348716"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

Gibt den Netzwerk Endpunkt (DNS-Name) des aktuellen Clusters zurück, der abgefragt wird.

## <a name="syntax"></a>Syntax

`current_cluster_endpoint()`

## <a name="returns"></a>Rückgabe

Der Netzwerk Endpunkt (DNS-Name) des aktuellen Clusters, der abgefragt wird, als Wert des Typs `string` .

## <a name="example"></a>Beispiel

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```