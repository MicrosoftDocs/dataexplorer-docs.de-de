---
title: 'extract(): Azure Data Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird extract() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 483c926d60abef120de2a355a6fa040b9608cd7a
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513045"
---
# <a name="extract"></a>extract()

Ruft eine Übereinstimmung für einen [regulären Ausdruck](./re2.md) aus einer Textzeichenfolge ab. 

Konvertiert optional die extrahierte Teilzeichenfolge in den angegebenen Typ.

```kusto
extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"
```

## <a name="syntax"></a>Syntax

`extract(`*regex*`,` *captureGroup*`,` *text* [`,` *typeLiteral*]`)`

## <a name="arguments"></a>Argumente

* *regex*: Ein [regulärer Ausdruck](./re2.md).
* *captureGroup*: Eine positive `int`-Konstante, die die zu extrahierende Erfassungsgruppe angibt. 0 steht für die vollständige Übereinstimmung, 1 für den mit der ersten „("Klammer")“ übereinstimmenden Wert im regulären Ausdruck, 2 oder höher für nachfolgende Klammern.
* *text*: Ein zu suchendes `string`-Element.
* *typeLiteral*: Ein optionales Typliteral (z. B. `typeof(long)`). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert. 

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