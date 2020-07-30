---
title: Rand ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Rand () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53fc7512c1a0fb2019526f48ade54fabbd351d05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345928"
---
# <a name="rand"></a>rand()

Gibt eine Zufallszahl zurück.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>Syntax

* `rand()`-Gibt einen Wert vom Typ `real` mit einer einheitlichen Verteilung im Bereich [0,0, 1,0) zurück.
* `rand(`*N* `)` -gibt einen Wert vom Typ zurück `real` , der mit einer einheitlichen Verteilung aus dem Satz {0,0, 1,0,..., *N* -1} gewählt wurde.