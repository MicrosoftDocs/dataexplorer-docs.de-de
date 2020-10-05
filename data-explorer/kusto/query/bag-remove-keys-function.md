---
title: bag_remove_keys ()-Azure Daten-Explorer
description: In diesem Artikel wird bag_remove_keys () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/04/2020
ms.openlocfilehash: fffbda419ac123af8e2744b76c7acbe560c07ce9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712342"
---
# <a name="bag_remove_keys"></a>bag_remove_keys ()

Entfernt Schlüssel und zugehörige Werte aus einem `dynamic` Eigenschaften Behälter.

## <a name="syntax"></a>Syntax

`bag_remove_keys(`*bag* Behälter `, ` *Schlüssel*`)`

## <a name="arguments"></a>Argumente

* *Bag*: `dynamic` Eigenschaften Behälter Eingabe.
* *Keys*: das `dynamic` Array enthält Schlüssel, die aus der Eingabe entfernt werden sollen. Schlüssel beziehen sich auf die erste Ebene der Eigenschaften Sammlung.
Das Angeben von Schlüsseln auf den untergeordneten Ebenen wird nicht unterstützt.

## <a name="returns"></a>Rückgabe

Gibt einen `dynamic` Eigenschaften Behälter ohne angegebene Schlüssel und deren Werte zurück.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(input:dynamic)
[
    dynamic({'key1' : 123,     'key2': 'abc'}),
    dynamic({'key1' : 'value', 'key3': 42.0}),
]
| extend result=bag_remove_keys(input, dynamic(['key2', 'key4']))
```

|input|result|
|---|---|
|{<br>  "key1": 123,<br>  "key2": "ABC"<br>}|{<br>  "key1": 123<br>}|
|{<br>  "key1": "Value",<br>  "key3": 42,0<br>}|{<br>  "key1": "Value",<br>  "key3": 42,0<br>}|
