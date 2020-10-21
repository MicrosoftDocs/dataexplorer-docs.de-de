---
title: current_cluster_endpoint ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird current_cluster_endpoint () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd35837b0c1affbb2101e6c71a4fa638ef8e466
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252565"
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