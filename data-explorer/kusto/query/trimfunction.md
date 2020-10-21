---
title: Trim ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Trim () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5a8bf4884bf6c493c1b3b960fce64fe143ed52e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251897"
---
# <a name="trim"></a>trim()

Entfernt alle führenden und nachfolgenden Übereinstimmungen des angegebenen regulären Ausdrucks.

## <a name="syntax"></a>Syntax

`trim(`*Regex* `,` *Text*`)`

## <a name="arguments"></a>Argumente

* *Regex*: Zeichenfolge oder [regulärer Ausdruck](re2.md) *, der am*Anfang und/oder am Ende des Texts abgeschnitten werden soll.  
* *Text*: eine Zeichenfolge.

## <a name="returns"></a>Rückgabe

*Text* nach dem Kürzen der Übereinstimmungen von *Regex* , die am Anfang und/oder am *Ende des Texts*gefunden wurden.

## <a name="example"></a>Beispiel

Die *Teil Zeichenfolge*  der Anweisungs untergeordneten Knoten vom Start und vom Ende der *string_to_trim*:

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

Die nächste Anweisung entfernt alle nicht-Wort Zeichen vom Anfang und Ende der Zeichenfolge:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-Te ST1//$|Te ST1|
|-Te ST2//$|Te ST2|
|-Te ST3//$|Te ST3|
|-Te ST4//$|Te-ST4|
|-Te ST5//$|Te ST5|


 