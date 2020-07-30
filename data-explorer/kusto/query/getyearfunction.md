---
title: getYear ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird getYear () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 032cc319661218e77d5b23e6c649de7d5856d6c9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347645"
---
# <a name="getyear"></a>getyear()

Gibt den Jahres Teil des `datetime` Arguments zurück.

## <a name="example"></a>Beispiel

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```