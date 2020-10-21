---
title: monthosyear ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird monthosyear () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd40612236b9c9b249c9c070bc784c518be0faae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250988"
---
# <a name="monthofyear"></a>monthofyear()

Gibt die Ganzzahl zurück, die die Monatsnummer des angegebenen Jahrs darstellt.

Ein anderer Alias: getMonth ()

```kusto
monthofyear(datetime("2015-12-14"))
```

## <a name="syntax"></a>Syntax

`monthofyear(`*a_date*`)`

## <a name="arguments"></a>Argumente

* `a_date`: Ein `datetime`-Wert.

## <a name="returns"></a>Rückgabe

`month number` des angegebenen Jahres.