---
title: extract_all ()-Azure Daten-Explorer
description: In diesem Artikel wird extract_all () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 85c118e8cd68c52278a34080eda4936151600cd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247528"
---
# <a name="extract_all"></a>extract_all()

Alle Übereinstimmungen für einen [regulären Ausdruck](./re2.md) aus einer Text Zeichenfolge erhalten.
Rufen Sie optional eine Teilmenge der übereinstimmenden Gruppen ab.

```kusto
print extract_all(@"(\d+)", "a set of numbers: 123, 567 and 789") // results with the dynamic array ["123", "567", "789"]
```

## <a name="syntax"></a>Syntax

`extract_all(`*Regex* `,` [*capturegroups* `,` ] *Text*`)`

## <a name="arguments"></a>Argumente

|Argument        |BESCHREIBUNG                                  |Erforderlich oder optional  |
|----------------|---------------------------------------------|----------------------|
|regex           | Ein [regulärer Ausdruck](./re2.md). Der Ausdruck muss mindestens eine Erfassungs Gruppe und kleiner oder gleich 16 Erfassungs Gruppen aufweisen.                                                         |Erforderlich              |
|capturegroups   |Eine dynamische Array Konstante, die die Erfassungs Gruppe angibt, die extrahiert werden soll. Gültige Werte liegen zwischen 1 und der Anzahl der Erfassungs Gruppen im regulären Ausdruck. Benannte Erfassungs Gruppen sind ebenfalls zulässig (siehe [Beispiele](#examples)).|Optional         |
|text            |Eine `string` zu durchsuchende                         |Erforderlich              |

## <a name="returns"></a>Rückgabe

* Wenn *Regex* eine Übereinstimmung im *Text*findet: gibt ein dynamisches Array zurück, das alle Übereinstimmungen mit den angezeigten Erfassungs Gruppen *capturegroups*oder alle *Erfassungs Gruppen in Regex*enthält.
* Wenn die Anzahl von *capturegroups* 1 ist: das zurückgegebene Array verfügt über eine einzelne Dimension von übereinstimmenden Werten.
* Wenn die Anzahl von *capturegroups* größer als 1 ist, ist das zurückgegebene Array eine zweidimensionale Auflistung von Übereinstimmungen mit mehreren Werten pro *capturegroups* -Auswahl oder alle Erfassungs Gruppen, die in *Regex* vorhanden sind, wenn *capturegroups* weggelassen werden.
* Wenn keine Entsprechung vorhanden ist: `null` .

## <a name="examples"></a>Beispiele

### <a name="extract-a-single-capture-group"></a>Extrahieren einer einzelnen Erfassungs Gruppe

Gibt eine Hex-Byte-Darstellung (zwei hexadezimale Ziffern) der GUID zurück.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"([\da-f]{2})", Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|["82", "B8", "be", "2D", "DF", "a7", "4B", "D1", "8F", "63", "24", "AD", "26", "D3", "14", "49"]|

### <a name="extract-several-capture-groups"></a>Extrahieren mehrerer Erfassungs Gruppen 

Verwendet einen regulären Ausdruck mit drei Erfassungs Gruppen, um die einzelnen GUID-Teile in den ersten Buchstaben, den letzten Buchstaben und den Wert in der Mitte aufzuteilen.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", Id)
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]]|

### <a name="extract-a-subset-of-capture-groups"></a>Extrahieren einer Teilmenge von Erfassungs Gruppen

Zeigt, wie eine Teilmenge der Erfassungs Gruppen ausgewählt wird. Der reguläre Ausdruck entspricht dem ersten Buchstaben, dem letzten Buchstaben und allen anderen. Der *capturegroups* -Parameter wird verwendet, um nur die ersten und letzten Teile auszuwählen.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(\w)(\w+)(\w)", dynamic([1,3]), Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|[["8", "d"], ["d", "7"], ["4", "1"], ["8", "3"], ["2", "9"]]|

### <a name="using-named-capture-groups"></a>Verwenden von benannten Erfassungs Gruppen

Sie können benannte Erfassungs Gruppen von RE2 in extract_all () verwenden.
Die *capturegroups* verwenden sowohl Aufzeichnungs Gruppen Indizes als auch einen benannten Erfassungs Gruppen Verweis zum Abrufen von übereinstimmenden Werten.

```kusto
print Id="82b8be2d-dfa7-4bd1-8f63-24ad26d31449"
| extend guid_bytes = extract_all(@"(?P<first>\w)(?P<middle>\w+)(?P<last>\w)", dynamic(['first',2,'last']), Id) 
```

|id|guid_bytes|
|---|---|
|82b8be2d-dfa7-4BD1-8F 63-24ad26d31449|[["8", "2b8be2", "d"], ["d", "FA", "7"], ["4", "BD", "1"], ["8", "F6", "3"], ["2", "4ad26d3144", "9"]]|
