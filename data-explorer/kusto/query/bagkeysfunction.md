---
title: bag_keys() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden bag_keys() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a73798dff27bf9f4c7d554d97804aef6b49e1de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518206"
---
# <a name="bag_keys"></a>bag_keys()

Zählt alle Stammschlüssel in einem dynamischen Property-Bag-Objekt auf.

`bag_keys(`*Dynamisches Objekt*`)`

**Rückgabe**

Ein Array von Schlüsseln, die Reihenfolge ist unbestimmt.

**Beispiele**

|Ausdruck|Auswertungsergebnis|
|---|---|
|`bag_keys(dynamic({'a':'b', 'c':123}))` | `['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':{'d':123}})) `|`['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':[{'d':123}]})) `|`['a','c']`|
|`bag_keys(dynamic(null))`|`null`|
|`bag_keys(dynamic({}))`|`[]`|
|`bag_keys(dynamic('a'))`|`null`|
|`bag_keys(dynamic([]))  `|`null`|