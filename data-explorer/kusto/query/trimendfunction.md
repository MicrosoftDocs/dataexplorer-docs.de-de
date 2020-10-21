---
title: trim_end ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird trim_end () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6a764752f126408ceb48f1c4a1af5c74014b6eab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251986"
---
# <a name="trim_end"></a>trim_end()

Entfernt die nachfolgende Entsprechung des angegebenen regulären Ausdrucks.

## <a name="syntax"></a>Syntax

`trim_end(`*Regex* `,` *Text*`)`

## <a name="arguments"></a>Argumente

* *Regex*: Zeichenfolge oder [regulärer Ausdruck](re2.md) *, der am Ende des Texts*abgeschnitten werden soll.  
* *Text*: eine Zeichenfolge.

## <a name="returns"></a>Rückgabe

*Text* nach dem *kürzen der überein*Stimmungen von *Regex* , der am Ende des Texts gefunden wurde.

## <a name="example"></a>Beispiel

Die *Teil Zeichenfolge*  der Anweisung untergeordneter Knoten vom Ende *string_to_trim*:

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing          |

Die nächste Anweisung entfernt alle nicht-Wort Zeichen am Ende der Zeichenfolge:

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|-Te ST1//$|-Te ST1  |
|-Te ST2//$|-Te ST2  |
|-Te ST3//$|-Te ST3  |
|-Te ST4//$|-Te-ST4  |
|-Te ST5//$|-Te ST5  |