---
title: dayosmonth ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird dayosmonth () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3341f416642b06d899c2a3d1f6675f4d3254291f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348495"
---
# <a name="dayofmonth"></a>dayofmonth()

Gibt die ganzzahlige Zahl zurück, die die Tagesnummer des angegebenen Monats darstellt.

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

## <a name="syntax"></a>Syntax

`dayofmonth(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Gibt zurück

`day number`des angegebenen Monats.