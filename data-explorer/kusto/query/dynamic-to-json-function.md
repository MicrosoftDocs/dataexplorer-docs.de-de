---
title: dynamic_to_json ()-Azure Daten-Explorer
description: In diesem Artikel wird dynamic_to_json () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 94901a69b4c4435e983f39f1692cf45cf27756e5
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793792"
---
# <a name="dynamic_to_json"></a>dynamic_to_json()

Konvertiert `dynamic` Eingaben in eine Zeichen folgen Darstellung.
Wenn die Eingabe ein Eigenschaften Behälter ist, druckt die Ausgabe Zeichenfolge ihren Inhalt rekursiv sortiert nach den Schlüsseln. Andernfalls ähnelt die Ausgabe der `tostring` funktionsausgabe.

## <a name="syntax"></a>Syntax

`dynamic_to_json(Expr)`

## <a name="arguments"></a>Argumente

* *Expr*: `dynamic` Input. Die Funktion akzeptiert ein Argument.

## <a name="returns"></a>Gibt zurück

Gibt eine Zeichen folgen Darstellung der `dynamic` Eingabe zurück. Wenn die Eingabe ein Eigenschaften Behälter ist, druckt die Ausgabe Zeichenfolge ihren Inhalt rekursiv sortiert nach den Schlüsseln.

## <a name="examples"></a>Beispiele

Ausdruck:

```kusto
  let bag1 = dynamic_to_json(dynamic({ 'Y10':dynamic({ }), 'X8': dynamic({ 'c3':1, 'd8':5, 'a4':6 }),'D1':114, 'A1':12, 'B1':2, 'C1':3, 'A14':[15, 13, 18]}));
  print bag1
```
  
Ergebnis:

```
"{
  ""A1"": 12,
  ""A14"": [
    15,
    13,
    18
  ],
  ""B1"": 2,
  ""C1"": 3,
  ""D1"": 114,
  ""X8"": {
    ""c3"": 1,
    ""d8"": 5,
    ""a4"": 6
  },
  ""Y10"": {}
}"
```

Ausdruck:

```kusto
 let bag2 = dynamic_to_json(dynamic({ 'X8': dynamic({ 'a4':6, 'c3':1, 'd8':5}), 'A14':[15, 13, 18], 'C1':3, 'B1':2, 'Y10': dynamic({ }), 'A1':12, 'D1':114}));
 print bag2
```
 
Ergebnis:

```
{
  "A1": 12,
  "A14": [
    15,
    13,
    18
  ],
  "B1": 2,
  "C1": 3,
  "D1": 114,
  "X8": {
    "a4": 6,
    "c3": 1,
    "d8": 5
  },
  "Y10": {}
}
```
