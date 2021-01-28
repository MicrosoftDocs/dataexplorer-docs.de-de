---
title: Funktionen „todynamic()“ und „parse_json()“ – Azure Data Explorer
description: In diesem Artikel werden die Funktionen „todynamic()“ und „parse_json()“ in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/25/2021
ms.localizationpriority: high
ms.openlocfilehash: 680ce93ec2a3b39d14475689ac80793a02d86547
ms.sourcegitcommit: 8468c6886dc097032ef6db86992a5e8c9b18786e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98911943"
---
# <a name="todynamic-parse_json"></a>todynamic(), parse_json()

Interpretiert `string` als JSON-Wert und gibt den Wert als `dynamic` zurück. 

> [!NOTE]
> Die Funktionen `todynamic()` und `parse_json()` werden gleichwertig interpretiert.

Diese Funktion ist der [extractjson()](./extractjsonfunction.md)-Funktion vorzuziehen, wenn Sie mehrere Elemente eines zusammengesetzten JSON-Objekts extrahieren müssen. Verwenden Sie nach Möglichkeit [dynamic()](./scalar-data-types/dynamic.md).

## <a name="syntax"></a>Syntax

`parse_json(`*json*`)`
`todynamic(`*json*`)`

<!-- deprecated aliases: `toobject()` and parsejson() -->

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
