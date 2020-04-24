---
title: hourofday() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt hourofday() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 08fdb45af5eec7f71d491725ea58a7d72c06371b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514075"
---
# <a name="hourofday"></a>hourofday()

Gibt die ganze Zahl zurück, die die Stundennummer des angegebenen Datums darstellt.

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

**Syntax**

`hourofday(`*a_date*`)`

**Argumente**

* `a_date`: Ein `datetime`-Wert.

**Rückgabe**

`hour number`des Tages (0-23).