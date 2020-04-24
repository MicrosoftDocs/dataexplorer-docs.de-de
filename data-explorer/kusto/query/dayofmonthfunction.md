---
title: dayofmonth() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt dayofmonth() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 791d85d4a8f89487e65ef68ecc605f907e63e1ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516336"
---
# <a name="dayofmonth"></a>dayofmonth()

Gibt die ganze Zahl zurück, die die Tagesnummer des angegebenen Monats darstellt.

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

**Syntax**

`dayofmonth(`*a_date*`)`

**Argumente**

* `a_date`: Ein `datetime`-Wert.

**Rückgabe**

`day number`des angegebenen Monats.