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
ms.openlocfilehash: cf21443beffb327e2708b8990017ac37fbbc8d21
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902041"
---
# <a name="buildschema-aggregation-function"></a>Buildschema () (Aggregations Funktion)

Gibt das minimale Schema zurück, das alle Werte von *dynamicexpr*zulässt.

* Kann nur im Kontext der Aggregation verwendet werden [, in zusammen](summarizeoperator.md) Fassung

**Syntax**

`buildschema(` *dynamicexpr* zusammenfassen`)`

**Argumente**

* *Dynamicexpr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. Der Parameter Spaltentyp muss lauten `dynamic` . 

**Rückgabe**

Der maximale Wert von *`Expr`* in der Gruppe.

> [!TIP] 
> Wenn `buildschema(json_column)` einen Syntax Fehler ergibt: *ist eine `json_column` Zeichenfolge anstelle eines dynamischen Objekts?* Verwenden Sie dann `buildschema(parsejson(json_column))` .

**Beispiel**

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
    
