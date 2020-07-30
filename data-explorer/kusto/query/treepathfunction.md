---
title: TreePath ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird TreePath () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e52caa6da7d5746119e363d1676b39fcd9da0340
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350654"
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