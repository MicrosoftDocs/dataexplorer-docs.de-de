---
title: strcmp() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt strcmp() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62ebcbfa71ebf8a29f3a8a1559feb91f96e0a2bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506901"
---
# <a name="strcmp"></a>strcmp()

Vergleicht zwei Zeichenfolgen.

Die Funktion beginnt mit dem Vergleich des ersten Zeichens jeder Zeichenfolge. Wenn sie gleich sind, wird mit den folgenden Paaren fortgesetzt, bis sich die Zeichen unterscheiden oder bis das Ende einer kürzeren Zeichenfolge erreicht ist.

**Syntax**

`strcmp(`*string1* `,` *string2*`)` 

**Argumente**

* *string1*: erste Eingabezeichenfolge zum Vergleich. 
* *string2*: zweite Eingabezeichenfolge zum Vergleich.

**Rückgabe**

Gibt einen integralen Wert zurück, der die Beziehung zwischen den Zeichenfolgen angibt:
* *<0* - das erste Zeichen, das nicht übereinstimmt, hat in String1 einen niedrigeren Wert als in string2
* *0* - der Inhalt beider Zeichenfolgen ist gleich
* *>0* - das erste Zeichen, das nicht übereinstimmt, hat in String1 einen größeren Wert als in string2

**Beispiele**

```
datatable(string1:string, string2:string)
["ABC","ABC",
"abc","ABC",
"ABC","abc",
"abcde","abc"]
| extend result = strcmp(string1,string2)
```

|string1|string2|result|
|---|---|---|
|ABC|ABC|0|
|abc|ABC|1|
|ABC|abc|-1|
|Abcde|abc|1|