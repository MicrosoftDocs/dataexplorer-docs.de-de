---
title: Extract ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Extract () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 63b6e966724efc92431b5a7017144a188c81f8d3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253000"
---
# <a name="extract"></a>extract()

Ruft eine Übereinstimmung für einen [regulären Ausdruck](./re2.md) aus einer Textzeichenfolge ab. 

Konvertieren Sie optional die extrahierte Teil Zeichenfolge in den bestimmten Typ.

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>Syntax

`extract(`*Regex* `,` *capturegroup* `,` *Text* [ `,` *typeliteral*]`)`

## <a name="arguments"></a>Argumente

* *Regex*: ein [regulärer Ausdruck](./re2.md).
* *capturegroup*: eine positive `int` Konstante, die die Erfassungs Gruppe angibt, die extrahiert werden soll. 0 steht für die vollständige Übereinstimmung, 1 für den mit der ersten „("Klammer")“ übereinstimmenden Wert im regulären Ausdruck, 2 oder höher für nachfolgende Klammern.
* *Text*: ein `string` zu durch Suchvorgang.
* *typeliteral*: Optionales typliterale (z. b. `typeof(long)` ). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert. 

## <a name="returns"></a>Rückgabe

Wenn mit *regex* eine Übereinstimmung in *text* gefunden wird: die mit der angegebenen Erfassungsgruppe *captureGroup* abgeglichene Teilzeichenfolge, optional konvertiert in *typeLiteral*.

Wenn keine Übereinstimmung vorhanden ist oder bei der Typkonvertierung ein Fehler auftritt: `null`. 

## <a name="examples"></a>Beispiele

Die Beispielzeichenfolge `Trace` wird nach einer Definition für `Duration` durchsucht. Die Übereinstimmung wird in `real` konvertiert und dann mit einer Zeitkonstanten (`1s`) multipliziert, damit `Duration` den Typ `timespan` erhält. In diesem Beispiel entspricht dies 123,45 Sekunden:

```kusto
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Dieses Beispiel entspricht `substring(Text, 2, 4)`:

```kusto
extract("^.{2,2}(.{4,4})", 1, Text)
```