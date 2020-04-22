---
title: parse_json() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird parse_json() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87c2155864f039fb5b261786d0fa6eb6770af2f
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744683"
---
# <a name="parse_json"></a>parse_json()

Interpretiert einen `string` als JSON-Wert und `dynamic`gibt den Wert als zurück. 

Es ist der Verwendung der [extractjson()-Funktion](./extractjsonfunction.md) überlegen, wenn Sie mehr als ein Element eines JSON-Verbundobjekts extrahieren müssen.

**Syntax**

`parse_json(`*Json*`)`

Aliase:
- [todynamic()](./todynamicfunction.md)
- [toobject()](./todynamicfunction.md)

**Argumente**

* *json*: Ein `string`Ausdruck vom Typ , der einen [JSON-formatierten Wert](https://json.org/) `dynamic` darstellt, oder einen Ausdruck vom Typ [dynamic](./scalar-data-types/dynamic.md), der den tatsächlichen Wert darstellt.

**Rückgabe**

Ein Objekt `dynamic` des Typs, das durch den Wert von *json*bestimmt wird:
* Wenn *json* vom `dynamic`Typ ist, wird sein Wert wie-ist verwendet.
* Wenn *json* vom `string`Typ ist und eine [richtig formatierte JSON-Zeichenfolge](https://json.org/)ist, wird die Zeichenfolge analysiert, und der erzeugte Wert wird zurückgegeben.
* Wenn *json* vom `string`Typ ist, aber **keine** [ordnungsgemäß formatierte JSON-Zeichenfolge](https://json.org/)ist, `dynamic` ist der `string` zurückgegebene Wert ein Objekt des Typs, das den ursprünglichen Wert enthält.

**Beispiel**

Für das folgende Beispiel gilt: Wenn `context_custom_metrics` ein `string`-Element ist, das wie folgt aussieht: 

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

dann ruft das folgende CSL-Fragment `duration` den Wert des Slots im Objekt ab, `duration.min` `118.0` und `110.0`daraus werden zwei Steckplätze `duration.value` bzw. ( bzw. ) abgerufen.

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Hinweise**

Es ist etwas üblich, eine JSON-Zeichenfolge zu haben, die eine Eigenschaftstasche beschreibt, in der einer der "Slots" eine andere JSON-Zeichenfolge ist. Beispiel:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

In solchen Fällen ist es nicht `parse_json` nur notwendig, zweimal zu rufen, `tostring` sondern auch sicherzustellen, dass im zweiten Aufruf verwendet wird. Andernfalls gibt der `parse_json` zweite Aufruf einfach die Eingabe an die Ausgabe weiter, `dynamic`da ihr deklarierter Typ:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```