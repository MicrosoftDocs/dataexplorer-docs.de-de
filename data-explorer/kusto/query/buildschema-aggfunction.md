---
title: 'Buildschema () (Aggregations Funktion): Azure-Daten-Explorer'
description: Dieser Artikel beschreibt Buildschema () (Aggregations Funktion) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d371b205363f320f666cfd92329219bb992fc69b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245271"
---
# <a name="buildschema-aggregation-function"></a>Buildschema () (Aggregations Funktion)

Gibt das minimale Schema zurück, das alle Werte von *dynamicexpr*zulässt.

* Kann nur im Kontext der Aggregation verwendet werden [, in zusammen](summarizeoperator.md) Fassung

## <a name="syntax"></a>Syntax

`buildschema(` *dynamicexpr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Dynamicexpr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. Der Parameter Spaltentyp muss lauten `dynamic` . 

## <a name="returns"></a>Rückgabe

Der maximale Wert von *`Expr`* in der Gruppe.

> [!TIP] 
> Wenn `buildschema(json_column)` einen Syntax Fehler ergibt:
>
> > *Handelt es sich um `json_column` eine Zeichenfolge anstatt um ein dynamisches Objekt?*
>
> Verwenden Sie dann `buildschema(parsejson(json_column))` .

## <a name="example"></a>Beispiel

Angenommen, die Eingabe Spalte hat drei dynamische Werte.

* `{"x":1, "y":3.5}`
* `{"x":"somevalue", "z":[1, 2, 3]}`
* `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`

Lautet das resultierende Schema folgendermaßen:

```kusto
{ 
    "x":["int", "string"],
    "y":["double", {"w": "string"}],
    "z":{"`indexer`": ["int", "string"]},
    "t":{"`indexer`": "string"}
}
```

Das Schema weist auf Folgendes hin:

* Das Stamm Objekt ist ein Container mit vier Eigenschaften mit den Namen x, y, z und t.
* Die Eigenschaft mit dem Namen "x", die vom Typ "int" oder vom Typ "String" sein kann.
* Die Eigenschaft mit dem Namen "y", die den Typ "Double" aufweisen kann, oder ein anderer Container mit der Eigenschaft "w" vom Typ "String".
* Das ``indexer`` -Schlüsselwort gibt an, dass „z“ und „t“ Arrays sind.
* Jedes Element im Array "z" ist vom Typ "int" oder vom Typ "String".
* „t“ ist ein Array von Zeichenfolgen.
* Jede Eigenschaft ist implizit optional, und jedes Array kann leer sein.

### <a name="schema-model"></a>Schemamodell

Die Syntax des zurückgegebenen Schemas lautet folgendermaßen:

```output
Container ::= '{' Named-type* '}';
Named-type ::= (name | '"`indexer`"') ':' Type;
Type ::= Primitive-type | Union-type | Container;
Union-type ::= '[' Type* ']';
Primitive-type ::= "int" | "string" | ...;
```

Die Werte entsprechen einer Teilmenge der typescript-Typanmerkungen, die als dynamischer Kusto-Wert codiert sind. In TypeScript würde das Beispielschema folgendermaßen lauten:

```typescript
var someobject: 
{
    x?: (number | string),
    y?: (number | { w?: string}),
    z?: { [n:number] : (int | string)},
    t?: { [n:number]: string }
}
```
