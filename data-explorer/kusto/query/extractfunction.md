---
title: auszug () - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt extract() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 398c79ddcd362f3c09fea18accd082c0eb3b1fc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515367"
---
# <a name="extract"></a>extract()

Ruft eine Übereinstimmung für einen [regulären Ausdruck](./re2.md) aus einer Textzeichenfolge ab. 

Konvertieren Sie optional die extrahierte Teilzeichenfolge in den angegebenen Typ.

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

**Syntax**

`extract(`*regex* `,` *captureGroup-Text* `,` *text* [`,` *typeLiteral*]`)`

**Argumente**

* *regex*: Ein [regulärer Ausdruck](./re2.md).
* *captureGroup*: `int` Eine positive Konstante, die die zu extrahierende Erfassungsgruppe angibt. 0 steht für die vollständige Übereinstimmung, 1 für den mit der ersten „("Klammer")“ übereinstimmenden Wert im regulären Ausdruck, 2 oder höher für nachfolgende Klammern.
* *Text*: `string` A zu suchen.
* *typeLiteral*: Ein optionales Typliteral `typeof(long)`(z. B. ). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert. 

**Rückgabe**

Wenn mit *regex* eine Übereinstimmung in *text* gefunden wird: die mit der angegebenen Erfassungsgruppe *captureGroup* abgeglichene Teilzeichenfolge, optional konvertiert in *typeLiteral*.

Wenn keine Übereinstimmung vorhanden ist oder bei der Typkonvertierung ein Fehler auftritt: `null`. 

**Beispiele**

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