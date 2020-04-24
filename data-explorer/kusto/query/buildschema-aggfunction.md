---
title: buildschema() (Aggregationsfunktion) - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt buildschema() (Aggregationsfunktion) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d0faceae970034ae96ced2b8958af4f16cb5dff4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517288"
---
# <a name="buildschema-aggregation-function"></a>buildschema() (Aggregationsfunktion)

Gibt das minimale Schema zurück, das alle Werte von *DynamicExpr*zugibt.

* Kann nur im Kontext der Aggregation innerhalb [der Zusammenfassung](summarizeoperator.md) verwendet werden

**Syntax**

`buildschema(` *DynamicExpr* zusammenfassen`)`

**Argumente**

* *DynamicExpr*: Ausdruck, der für die Aggregationsberechnung verwendet wird. Der Parameterspaltentyp `dynamic`sollte . 

**Rückgabe**

Der maximale Wert von *Expr* in der gesamten Gruppe.

> [!TIP] 
> Wenn `buildschema(json_column)` ein Syntaxfehler angezeigt wird: *Ist Ihre `json_column` Zeichenfolge eher eine Zeichenfolge als ein dynamisches Objekt?* Wenn ja, müssen `buildschema(parsejson(json_column))`Sie .

**Beispiel**

Wenn die Eingabespalte drei dynamische Werte hat:

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

* Das Stammobjekt ist ein Container mit den vier Eigenschaften x, y, z und t.
* Die Eigenschaft „x“ kann entweder den Typ „int“ oder „string“ haben.
* Bei der Eigenschaft „y“ kann es sich entweder um den Typ „double“ handeln oder um einen anderen Container mit einer Eigenschaft namens „w“ vom Typ „string“.
* Das ``indexer`` -Schlüsselwort gibt an, dass „z“ und „t“ Arrays sind.
* Jedes Element im Array „z“ ist entweder eine ganze Zahl oder eine Zeichenfolge (string).
* „t“ ist ein Array von Zeichenfolgen.
* Jede Eigenschaft ist implizit optional, und jedes Array kann leer sein.

### <a name="schema-model"></a>Schemamodell

Die Syntax des zurückgegebenen Schemas lautet folgendermaßen:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Sie entsprechen einer Teilmenge der Typscript-Typanmerkungen, die als dynamischer Kusto-Wert codiert sind. In TypeScript würde das Beispielschema folgendermaßen lauten:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }