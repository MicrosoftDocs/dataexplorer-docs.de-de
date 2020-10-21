---
title: getYear ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird getYear () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a820d05b453be7cc991f8068644d33ff990115fc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251146"
---
# <a name="getyear"></a>getyear()

Gibt den Jahres Teil des `datetime` Arguments zurück.

## <a name="example"></a>Beispiel

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```