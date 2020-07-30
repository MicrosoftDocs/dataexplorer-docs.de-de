---
title: trim_end ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird trim_end () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cab78680a3b996234724bc052d75959928520289
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339849"
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

Die *Teil Zeichenfolge* der Anweisung untergeordneter Knoten vom Ende *string_to_trim*:

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