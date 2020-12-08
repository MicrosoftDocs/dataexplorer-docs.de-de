---
title: 'parse_json(): Azure Data Explorer'
description: In diesem Artikel wird parse_json() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 3125a51733f6672d041e6c1522ea755e5677cb0c
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512858"
---
# <a name="parse_json"></a>parse_json()

Interpretiert `string` als JSON-Wert und gibt den Wert als `dynamic` zurück.

Diese Funktion ist der [extractjson()](./extractjsonfunction.md)-Funktion vorzuziehen, wenn Sie mehrere Elemente eines zusammengesetzten JSON-Objekts extrahieren müssen.

## <a name="syntax"></a>Syntax

`parse_json(`*json*`)`

Aliase:
- [todynamic()](./todynamicfunction.md)
- [toobject()](./todynamicfunction.md)

## <a name="arguments"></a>Argumente

* *json*: Ein Ausdruck vom Typ `string`. Er stellt einen [JSON-formatierten Wert](https://json.org/) oder einen Ausdruck vom Typ [dynamic](./scalar-data-types/dynamic.md) dar, der den tatsächlichen `dynamic`-Wert darstellt.

## <a name="returns"></a>Gibt zurück

Ein Objekt vom Typ `dynamic`, das durch den Wert von *json* festgelegt wird:
* Wenn *json* vom Typ `dynamic` ist, wird der zugehörige Wert unverändert verwendet.
* Wenn *json* vom Typ `string` und eine [ordnungsgemäß formatierte JSON-Zeichenfolge](https://json.org/) ist, wird die Zeichenfolge analysiert und der generierte Wert zurückgegeben.
* Wenn *json* vom Typ `string`, aber keine [ordnungsgemäß formatierte JSON-Zeichenfolge](https://json.org/) ist, ist der zurückgegebene Wert ein Objekt vom Typ `dynamic`, das den ursprünglichen `string`-Wert enthält.

## <a name="example"></a>Beispiel

Für das folgende Beispiel gilt: Wenn `context_custom_metrics` ein `string`-Element ist, das wie folgt aussieht:

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

dann ruft das folgende CSL-Fragment zunächst den Wert des `duration`-Slots im Objekt und daraus zwei Slots ab: `duration.value` und `duration.min` (bzw. `118.0` und `110.0`).

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Hinweise**

Es kommt häufig vor, dass eine JSON-Zeichenfolge einen Eigenschaftenbehälter beschreibt, in dem einer der „Slots“ eine weitere JSON-Zeichenfolge ist. 

Zum Beispiel:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

In solchen Fällen ist es nur erforderlich, `parse_json` zwei Mal aufzurufen. Jedoch muss sichergestellt werden, dass im zweiten Aufruf `tostring` verwendet wird. Andernfalls übergibt der zweite Aufruf von `parse_json` einfach die Eingabe unverändert an die Ausgabe, da der deklarierte Typ `dynamic` ist.

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
