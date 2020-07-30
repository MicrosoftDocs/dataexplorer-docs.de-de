---
title: monthosyear ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird monthosyear () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af015146bb2f07d83d4333312a96d5b80c67190a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346727"
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

`month number`des angegebenen Jahres.