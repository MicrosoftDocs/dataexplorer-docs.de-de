---
title: rand() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt rand() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3638d4b979b7318f58efec0bed0da4c31896a9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510641"
---
# <a name="rand"></a>rand()

Gibt eine Zufallszahl zurück.

```kusto
rand()
rand(1000)
```

**Syntax**

* `rand()`- gibt einen `real` Typwert mit einer gleichmäßigen Verteilung im Bereich [0.0, 1.0) zurück.
* `rand(`*N* `)` - Gibt einen `real` Wert des Typs zurück, der mit einer gleichmäßigen Verteilung aus dem Satz '0.0, 1.0, ..., *N* - 1' ausgewählt wurde.