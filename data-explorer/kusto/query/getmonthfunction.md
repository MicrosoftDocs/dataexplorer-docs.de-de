---
title: GetMonth ()-Azure Daten-Explorer
description: In diesem Artikel wird getMonth () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: 13254314bdbab7ddbdb74341e384867c26b6259d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347696"
---
# <a name="getmonth"></a>getmonth()

Rufen Sie die Monatsnummer (1-12) aus einem datetime-Wert ab.

Ein anderer Alias: monthoyear ()

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
