---
title: trim_start ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird trim_start () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4550fb07da37658ecf11a4eb04ecdf199d8ba989
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339543"
---
# <a name="trim_start"></a>trim_start()

Entfernt die führende Entsprechung des angegebenen regulären Ausdrucks.

## <a name="syntax"></a>Syntax

`trim_start(`*Regex* `,` *Text*`)`

## <a name="arguments"></a>Argumente

* *Regex*: Zeichenfolge oder [regulärer Ausdruck](re2.md) *, der vom*Anfang des Texts abgeschnitten werden soll.  
* *Text*: eine Zeichenfolge.

## <a name="returns"></a>Rückgabe

*Text* nach dem Kürzen der Übereinstimmung von *Regex* , der am Anfang des Texts gefunden *wurde.*

## <a name="example"></a>Beispiel

Die *Teil Zeichenfolge* der Anweisung untergeordneter Knoten vom Anfang *string_to_trim*:

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

Die nächste Anweisung entfernt alle nicht-Wort Zeichen am Anfang der Zeichenfolge:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-Te ST1//$|Te ST1//$|
|-Te ST2//$|Te ST2//$|
|-Te ST3//$|Te ST3//$|
|-Te ST4//$|Te-ST4//$|
|-Te ST5//$|Te ST5//$|

 