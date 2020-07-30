---
title: Assert ()-Azure-Daten-Explorer
description: Dieser Artikel beschreibt Assert () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: a477a8fd8e05bd6420f06c28f71f72431a343a31
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349464"
---
# <a name="assert"></a>assert()

Überprüft eine Bedingung. Wenn die Bedingung false lautet, werden Fehlermeldungen ausgegeben, und die Abfrage schlägt fehl.

## <a name="syntax"></a>Syntax

`assert(`*Bedingung* `, ` *Nachricht*`)`

## <a name="arguments"></a>Argumente

* *Condition*: Der auszuwertende bedingte Ausdruck. Wenn die Bedingung ist `false` , wird die angegebene Meldung verwendet, um einen Fehler zu melden. Wenn die Bedingung ist `true` , wird `true` als Auswertungs Ergebnis zurückgegeben. Während der Abfrage Analysephase muss die Bedingung als konstant ausgewertet werden.
* *Message*: die Meldung, die verwendet wird, wenn die-Assertionen ausgewertet werden `false` Die *Nachricht* muss ein zeichenfolgenliteralsein.


## <a name="returns"></a>Rückgabe

* `true`-Wenn die Bedingung ist`true`
* Löst einen semantischen Fehler aus, wenn die Bedingung als ausgewertet wird `false` .

**Hinweise**

* `condition`muss in der Phase der Abfrage Analyse als konstant ausgewertet werden. Das heißt, Sie kann aus anderen Ausdrücken erstellt werden, die auf Konstanten verweisen und nicht an den Zeilen Kontext gebunden werden können.

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
