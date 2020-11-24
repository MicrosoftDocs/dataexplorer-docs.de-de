---
title: parse_json ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_json () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 3125a51733f6672d041e6c1522ea755e5677cb0c
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512858"
---
# <a name="parse_json"></a>parse_json()

Interpretiert einen `string` als JSON-Wert und gibt den Wert als zurück `dynamic` .

Diese Funktion ist besser als die [extractjson ()-Funktion](./extractjsonfunction.md) , wenn Sie mehr als ein Element eines JSON-Verbund Objekts extrahieren müssen.

## <a name="syntax"></a>Syntax

`parse_json(`*json*`)`

Aliase:
- [todynamic()](./todynamicfunction.md)
- [Objekt ()](./todynamicfunction.md)

## <a name="arguments"></a>Argumente

* *JSON*: ein Ausdruck vom Typ `string` . Sie stellt einen [JSON-formatierten Wert](https://json.org/)oder einen Ausdruck vom Typ [Dynamic](./scalar-data-types/dynamic.md)dar, der den tatsächlichen `dynamic` Wert darstellt.

## <a name="returns"></a>Gibt zurück

Ein Objekt vom Typ `dynamic` , das durch den Wert von *JSON* bestimmt wird:
* Wenn *JSON* vom Typ ist `dynamic` , wird der zugehörige Wert unverändert verwendet.
* Wenn *JSON* vom Typ ist `string` und eine [ordnungsgemäß formatierte JSON-Zeichenfolge](https://json.org/)ist, wird die Zeichenfolge analysiert, und der erzeugte Wert wird zurückgegeben.
* Wenn *JSON* vom Typ ist `string` , aber keine [ordnungsgemäß formatierte JSON-Zeichenfolge](https://json.org/)ist, ist der zurückgegebene Wert ein Objekt vom Typ, das `dynamic` den ursprünglichen `string` Wert enthält.

## <a name="example"></a>Beispiel

Für das folgende Beispiel gilt: Wenn `context_custom_metrics` ein `string`-Element ist, das wie folgt aussieht:

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

dann ruft das folgende CSL-Fragment den Wert des `duration` Slots im-Objekt ab und ruft dann zwei Slots `duration.value` `duration.min` ( `118.0` `110.0` bzw. bzw.) ab.

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Hinweise**

Es kommt häufig vor, dass eine JSON-Zeichenfolge einen Eigenschaften Behälter beschreibt, in dem einer der "Slots" eine andere JSON-Zeichenfolge ist. 

Beispiel:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

In solchen Fällen ist es nicht erforderlich, `parse_json` zweimal aufzurufen, sondern auch sicherzustellen, dass im zweiten Aufruf `tostring` verwendet wird. Andernfalls übergibt der zweite-Befehl `parse_json` lediglich die Eingabe unverändert an die Ausgabe, da der deklarierte Typ ist `dynamic` .

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
