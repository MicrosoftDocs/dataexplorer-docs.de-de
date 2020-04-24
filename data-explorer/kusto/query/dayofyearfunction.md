---
title: dayofyear() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt dayofyear() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41e7c5906da001e877dd9124124e126d729e886d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516217"
---
# <a name="dayofyear"></a>dayofyear()

Gibt die ganze Zahl an, die die Tagesnummer des angegebenen Jahres darstellt.

```kusto
dayofyear(datetime(2015-12-14))
```

**Syntax**

`dayofweek(`*a_date*`)`

**Argumente**

* `a_date`: Ein `datetime`-Wert.

**Rückgabe**

`day number`des gegebenen Jahres.