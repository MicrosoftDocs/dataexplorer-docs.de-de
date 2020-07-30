---
title: 'Buildschema () (Aggregations Funktion): Azure-Daten-Explorer'
description: Dieser Artikel beschreibt Buildschema () (Aggregations Funktion) in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2520849508c9cef829d7c8c07f22d3f8c64cfcea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348937"
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
> Wenn `buildschema(json_column)` einen Syntax Fehler ergibt: *ist eine `json_column` Zeichenfolge anstelle eines dynamischen Objekts?* Verwenden Sie dann `buildschema(parsejson(json_column))` .

## <a name="example"></a>Beispiel

Angenommen, die Eingabe Spalte hat drei dynamische Werte.

||
|---|
|`{"x":1, "y":3.5}`|
|`{"x":"somevalue", "z":[1, 2, 3]}`|
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`|
||

Lautet das resultierende Schema folgendermaßen:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

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

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Die Werte entsprechen einer Teilmenge der typescript-Typanmerkungen, die als dynamischer Kusto-Wert codiert sind. In TypeScript würde das Beispielschema folgendermaßen lauten:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }
    
