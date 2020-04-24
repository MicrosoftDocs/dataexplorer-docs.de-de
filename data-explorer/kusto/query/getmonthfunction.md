---
title: getmonth() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt getmonth() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: b0224d8ea9c99ce72604a5b7df248394bc6317fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514415"
---
# <a name="getmonth"></a>getmonth()

Rufen Sie die Monatsnummer (1-12) aus einem datetime-Wert ab.

Ein weiterer Alias: monthoyear()

**Beispiel**

```kusto
print month = getmonth(datetime(2015-10-12))
```