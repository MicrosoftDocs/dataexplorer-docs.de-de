---
title: TreePath ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird TreePath () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 91e11e6d45f82e6e27ea2da5ca1da945f190b69f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251990"
---
# <a name="treepath"></a>treepath()

Listet alle Path-Ausdrücke auf, die Verzweigungen in einem dynamischen Objekt identifizieren.

`treepath(`*dynamisches Objekt*`)`

## <a name="returns"></a>Rückgabe

Ein Array von Path-Ausdrücken.

## <a name="examples"></a>Beispiele

|Ausdruck|Auswertungsergebnis|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|