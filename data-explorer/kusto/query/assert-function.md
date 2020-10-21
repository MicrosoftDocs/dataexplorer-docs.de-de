---
title: Assert ()-Azure-Daten-Explorer
description: Dieser Artikel beschreibt Assert () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 442fbec2742a4d1edc7a9ad03a81db27e6d18574
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252780"
---
# <a name="assert"></a>assert()

Überprüft eine Bedingung. Wenn die Bedingung false lautet, werden Fehlermeldungen ausgegeben, und die Abfrage schlägt fehl.

## <a name="syntax"></a>Syntax

`assert(`*Bedingung* `, ` *Nachricht*`)`

## <a name="arguments"></a>Argumente

* *Condition*: Der auszuwertende bedingte Ausdruck. Wenn die Bedingung ist `false` , wird die angegebene Meldung verwendet, um einen Fehler zu melden. Wenn die Bedingung ist `true` , wird `true` als Auswertungs Ergebnis zurückgegeben. Während der Abfrage Analysephase muss die Bedingung als konstant ausgewertet werden.
* *Message*: die Meldung, die verwendet wird, wenn die-Assertionen ausgewertet werden `false` Die *Nachricht* muss ein zeichenfolgenliteralsein.

> [!NOTE]
> `condition` muss in der Phase der Abfrage Analyse als konstant ausgewertet werden. Das heißt, Sie kann aus anderen Ausdrücken erstellt werden, die auf Konstanten verweisen und nicht an den Zeilen Kontext gebunden werden können.

## <a name="returns"></a>Rückgabe

* `true` -Wenn die Bedingung ist `true`
* Löst einen semantischen Fehler aus, wenn die Bedingung als ausgewertet wird `false` .

## <a name="examples"></a>Beispiele

Die folgende Abfrage definiert eine Funktion, `checkLength()` die die Länge der Eingabe Zeichenfolge überprüft, und verwendet `assert` , um den Eingabe Längen Parameter zu überprüfen (überprüft, ob er größer als NULL ist).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and 
    strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=long(-1), input)
```

Wenn Sie diese Abfrage ausführen, wird ein Fehler ausgegeben:  
`assert() has failed with message: 'Length must be greater than zero'`


Beispiel für die Ausführung mit gültiger `len` Eingabe:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let checkLength = (len:long, s:string)
{
    assert(len > 0, "Length must be greater than zero") and strlen(s) > len
};
datatable(input:string)
[
    '123',
    '4567'
]
| where checkLength(len=3, input)
```

|input|
|---|
|4567|
