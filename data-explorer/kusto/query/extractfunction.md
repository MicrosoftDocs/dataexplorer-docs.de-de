---
title: Extract ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Extract () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6239d4da8c2cf2b1faa56ec627928524beca9a56
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348053"
---
# <a name="extract"></a>extract()

Ruft eine Übereinstimmung für einen [regulären Ausdruck](./re2.md) aus einer Textzeichenfolge ab. 

Konvertieren Sie optional die extrahierte Teil Zeichenfolge in den bestimmten Typ.

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

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