---
title: GetMonth ()-Azure Daten-Explorer
description: In diesem Artikel wird getMonth () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: 1b3f87c7be8f4b2e12fc0136c163c4df0766eed8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252979"
---
# <a name="getmonth"></a>getmonth()

Rufen Sie die Monatsnummer (1-12) aus einem datetime-Wert ab.

Ein anderer Alias: monthoyear ()

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
