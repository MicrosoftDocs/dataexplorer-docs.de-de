---
title: Rand ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Rand () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1206f72b3951601c105de450bc6923861ad011ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241296"
---
# <a name="rand"></a>rand()

Gibt eine Zufallszahl zurück.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>Syntax

* `rand()` -Gibt einen Wert vom Typ `real` mit einer einheitlichen Verteilung im Bereich [0,0, 1,0) zurück.
* `rand(`*N* `)` -gibt einen Wert vom Typ zurück `real` , der mit einer einheitlichen Verteilung aus dem Satz {0,0, 1,0,..., *N* -1} gewählt wurde.