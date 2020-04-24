---
title: assert() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird assert() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f01df7bc85171eefabeb2ed835109f0faef81e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518410"
---
# <a name="assert"></a>assert()

Überprüft eine Bedingung. Wenn die Bedingung falsch ist, gibt fehlermeldungen aus und schlägt die Abfrage fehl.

**Syntax**

`assert(`*Zustandsmeldung*`, `*message*`)`

**Argumente**

* *Bedingung*: Der zu bewertende bedingte Ausdruck. Wenn die `false`Bedingung ist , wird die angegebene Meldung verwendet, um einen Fehler zu melden. Wenn die `true`Bedingung ist `true` , wird sie als Auswertungsergebnis zurückgegeben. Die Bedingung muss während der Abfrageanalysephase auf Konstante ausgewertet werden.
* *message*: Die Nachricht, die `false`verwendet wird, wenn die Assertion in ausgewertet wird. Die *Nachricht* muss ein Zeichenfolgenliteral sein.


**Rückgabe**

* `true`- wenn der Zustand`true`
* Löst einen semantischen Fehler aus, wenn die Bedingung auf `false`ausgewertet wird.

**Hinweise**

* `condition`muss während der Abfrageanalysephase auf Konstante ausgewertet werden. Mit anderen Worten, es kann aus anderen Ausdrücken erstellt werden, die auf Konstanten verweisen, und kann nicht an den Zeilenkontext gebunden werden.

**Beispiele**

Die folgende Abfrage definiert `checkLength()` eine Funktion, die `assert` die Länge der Eingabezeichenfolge überprüft und zum Überprüfen des Eingabelängenparameters verwendet (überprüft, ob sie größer als Null ist).

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

Das Ausführen dieser Abfrage führt zu einem Fehler:  
`assert() has failed with message: 'Length must be greater than zero'`


Beispiel für die `len` Ausführung mit gültiger Eingabe:

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