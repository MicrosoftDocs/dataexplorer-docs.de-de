---
title: "\"straucmp ()\": Azure Daten-Explorer | Microsoft-Dokumentation"
description: In diesem Artikel wird "straucmp ()" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 11951195d95d956f70d4bfce32d22a9f9c73dc3d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350926"
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