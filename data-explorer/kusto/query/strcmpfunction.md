---
title: "\"straucmp ()\": Azure Daten-Explorer | Microsoft-Dokumentation"
description: In diesem Artikel wird "straucmp ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5c1e4cb9a4ad77d0d48f52ca7e47fc6cea06cff4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243731"
---
# <a name="strcmp"></a>strcmp()

Vergleicht zwei Zeichenfolgen.

Die Funktion beginnt mit dem Vergleich des ersten Zeichens der einzelnen Zeichen folgen. Wenn Sie gleich sind, wird die Datei mit den folgenden Paaren fortgesetzt, bis die Zeichen unterschiedlich sind oder bis das Ende der kürzeren Zeichenfolge erreicht ist.

## <a name="syntax"></a>Syntax

`strcmp(`*Zeichenfolge1* `,` *Zeichenfolge2*`)` 

## <a name="arguments"></a>Argumente

* *Zeichenfolge1*: erste Eingabe Zeichenfolge für den Vergleich. 
* *Zeichenfolge2*: zweite Eingabe Zeichenfolge für den Vergleich.

## <a name="returns"></a>Rückgabe

Gibt einen integralen Wert zurück, der die Beziehung zwischen den Zeichen folgen angibt:
* *<0* -das erste Zeichen, das nicht entspricht, hat einen niedrigeren Wert in Zeichenfolge1 als in Zeichenfolge2
* *0* -der Inhalt beider Zeichen folgen ist gleich.
* *>0* -das erste Zeichen, das nicht entspricht, hat einen größeren Wert in Zeichenfolge1 als in Zeichenfolge2

## <a name="examples"></a>Beispiele

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
|abcde|abc|1|