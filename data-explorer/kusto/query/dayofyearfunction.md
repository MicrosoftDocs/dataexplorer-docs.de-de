---
title: dayosyear ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird dayosyear () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 925b65846c6ba32163bd325fd2ee3321bc7fc802
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348461"
---
# <a name="dayofyear"></a>dayofyear()

Gibt die Ganzzahl zurück, die die Tagesnummer des angegebenen Jahrs darstellt.

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>Syntax

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Rückgabe

`day number`des angegebenen Jahres.