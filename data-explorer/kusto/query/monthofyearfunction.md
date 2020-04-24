---
title: monthofyear() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt monthofyear() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ada34d3e6f6c905a1acfb550b02af3dab550fb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512341"
---
# <a name="monthofyear"></a>monthofyear()

Gibt die ganze Zahl an, die die Monatsnummer des angegebenen Jahres darstellt.

Ein weiterer Alias: getmonth()

```kusto
monthofyear(datetime("2015-12-14"))
```

**Syntax**

`monthofyear(`*a_date*`)`

**Argumente**

* `a_date`: Ein `datetime`-Wert.

**Rückgabe**

`month number`des gegebenen Jahres.