---
title: extract_all() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird extract_all() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7623a064e272f8b96ca25cc6af47318e26dfb93
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515418"
---
# <a name="extract_all"></a>extract_all()

Abrufen aller Übereinstimmungen für einen [regulären Ausdruck](./re2.md) aus einer Textzeichenfolge.

Optional kann eine Teilmenge übereinstimmender Gruppen abgerufen werden.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") == dynamic(["123", "567", "789"])
```

**Syntax**

`extract_all(`*regex* `,` [*captureGroups*`,`] *Text*`)`

**Argumente**

* *regex*: Ein [regulärer Ausdruck](./re2.md). Der reguläre Ausdruck muss mindestens eine Erfassungsgruppe und weniger oder gleich 16 Erfassungsgruppen haben.
* *captureGroups*: (optional). Eine dynamische Arraykonstante, die die zu extrahierende Erfassungsgruppe angibt. Gültige Werte liegen zwischen 1 und der Anzahl der Erfassungsgruppen im regulären Ausdruck. Benannte Erfassungsgruppen sind ebenfalls zulässig (siehe Abschnitt Beispiele).
* *Text*: `string` A zu suchen.

**Rückgabe**

Wenn *regex* eine Übereinstimmung im *Text*findet: Gibt dynamisches Array zurück, einschließlich aller Übereinstimmungen mit den angegebenen Erfassungsgruppen *captureGroups* (oder allen Erfassungsgruppen im *Regex*).
Wenn die Anzahl der *captureGroups* 1 ist: Das zurückgegebene Array hat eine einzelne Dimension übereinstimmender Werte.
Wenn die Anzahl der *captureGroups* größer als 1 ist: Das zurückgegebene Array ist eine zweidimensionale Auflistung von Übereinstimmungen mit mehreren Werten pro *captureGroups-Auswahl* (oder alle Erfassungsgruppen, die im *Regex* vorhanden sind, wenn *captureGroups* weggelassen wird). 

Wenn es keine Übereinstimmung `null`gibt: . 

**Beispiele**

### <a name="extracting-single-capture-group"></a>Extrahieren einer einzelnen Erfassungsgruppe
Im folgenden Beispiel wird die Hex-Byte-Darstellung (zwei Hex-Ziffern) der GUID zurückgegeben.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|["82","b8","be","2d","df","a7","4b","d1","8f","63","24","ad","26","d3","14","49"]|

### <a name="extracting-several-capture-groups"></a>Extrahieren mehrerer Erfassungsgruppen 
Im nächsten Beispiel wird ein regulärer Ausdruck mit 3 Erfassungsgruppen verwendet, um jedes GUID-Teil in den ersten Buchstaben, den letzten Buchstaben und was auch immer in der Mitte aufzuteilen.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8","2b8be2","d"],["d","fa","7"],["4","bd","1"],["8","f6","3"],["2","4ad26d3144","9"]]|

### <a name="extracting-subset-of-capture-groups"></a>Extrahieren einer Teilmenge von Erfassungsgruppen

Das nächste Beispiel zeigt, wie Sie eine Teilmenge von Erfassungsgruppen auswählen: In diesem Fall stimmt der reguläre Ausdruck in den ersten Buchstaben, den letzten Buchstaben und den Rest überein – während der Parameter *captureGroups* verwendet wird, um nur den ersten und den letzten Teil auszuwählen. 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8","d"],["d","7"],["4","1"],["8","3"],["2","9"]]|


### <a name="using-named-capture-groups"></a>Verwenden benannter Erfassungsgruppen

Sie können benannte Erfassungsgruppen von RE2 in extract_all() verwenden. Im folgenden Beispiel verwenden die *captureGroups* sowohl Erfassungsgruppenindizes als auch benannte Erfassungsgruppenverweise, um übereinstimmende Werte abzurufen.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4bd1-8f63-24ad26d31449|[["8","2b8be2","d"],["d","fa","7"],["4","bd","1"],["8","f6","3"],["2","4ad26d3144","9"]]|