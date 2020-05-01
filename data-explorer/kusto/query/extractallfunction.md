---
title: extract_all ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird extract_all () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 94bafd3890f57a1379440c5cced3fa349f9055c5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616421"
---
# <a name="extract_all"></a>extract_all()

Alle Übereinstimmungen für einen [regulären Ausdruck](./re2.md) aus einer Text Zeichenfolge erhalten.

Optional kann eine Teilmenge der übereinstimmenden Gruppen abgerufen werden.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

**Syntax**

`extract_all(`*Regex* `,` [*capturegroups*`,`] *Text*`)`

**Argumente**

* *Regex*: ein [regulärer Ausdruck](./re2.md). Der reguläre Ausdruck muss mindestens eine Erfassungs Gruppe und kleiner oder gleich 16 Erfassungs Gruppen aufweisen.
* *capturegroups*: (optional). Eine dynamische Array Konstante, die die zu Extra hier Erfassungs Gruppe angibt. Gültige Werte liegen zwischen 1 und der Anzahl der Erfassungs Gruppen im regulären Ausdruck. Benannte Erfassungs Gruppen sind ebenfalls zulässig (siehe Abschnitt "Beispiele").
* *Text*: ein `string` zu durch Suchvorgang.

**Rückgabe**

Wenn *Regex* eine Übereinstimmung im *Text*findet: gibt ein dynamisches Array zurück, einschließlich aller Übereinstimmungen mit den angezeigten Erfassungs Gruppen *capturegroups* (oder alle *Erfassungs Gruppen im Regex*).
Wenn die Anzahl von *capturegroups* 1 ist: das zurückgegebene Array verfügt über eine einzelne Dimension von übereinstimmenden Werten.
Wenn die Anzahl der *capturegroups* -Werte größer als 1 ist: das zurückgegebene Array ist eine zweidimensionale Auflistung von Übereinstimmungen mit mehreren Werten pro *capturegroups* -Auswahl (oder alle Erfassungs Gruppen, die in *Regex* vorhanden sind, wenn *capturegroups* weggelassen wird). 

Wenn keine Entsprechung vorhanden ist `null`:. 

**Beispiele**

### <a name="extracting-single-capture-group"></a>Extrahieren einer einzelnen Erfassungs Gruppe
Das folgende Beispiel gibt eine Hex-Byte-Darstellung (zwei hexadezimale Ziffern) der GUID zurück.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|["82", "B8", "be", "2D", "DF", "a7", "4B", "D1", "8F", "63", "24", "AD", "26", "D3", "14", "49"]|

### <a name="extracting-several-capture-groups"></a>Extrahieren mehrerer Erfassungs Gruppen 
Im nächsten Beispiel wird ein regulärer Ausdruck mit drei Erfassungs Gruppen verwendet, um die einzelnen GUID-Teile in den ersten Buchstaben, den letzten Buchstaben usw.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]]|

### <a name="extracting-subset-of-capture-groups"></a>Die Teilmenge der Erfassungs Gruppen wird extrahiert.

Im nächsten Beispiel wird gezeigt, wie eine Teilmenge der Erfassungs Gruppen ausgewählt wird: in diesem Fall entspricht der reguläre Ausdruck dem ersten Buchstaben, dem letzten Buchstaben und dem Rest, während der *capturegroups* -Parameter verwendet wird, um nur den ersten und den letzten Teil auszuwählen. 

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|[["8", "d"], ["d", "7"], ["4", "1"], ["8", "3"], ["2", "9"]]|


### <a name="using-named-capture-groups"></a>Verwenden von benannten Erfassungs Gruppen

Sie können benannte Erfassungs Gruppen von RE2 in extract_all () verwenden. Im folgenden Beispiel verwenden die *capturegroups* sowohl die Aufzeichnungs Gruppen Indizes als auch den benannten Erfassungs Gruppen Verweis zum Abrufen von übereinstimmenden Werten.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|Id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]]|
