---
title: Trim ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Trim () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a28ca267612bef68c676118331b3010a8c947e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350637"
---
# <a name="trim"></a>trim()

Entfernt alle führenden und nachfolgenden Übereinstimmungen des angegebenen regulären Ausdrucks.

## <a name="syntax"></a>Syntax

`trim(`*Regex* `,` *Text*`)`

## <a name="arguments"></a>Argumente

* *Regex*: Zeichenfolge oder [regulärer Ausdruck](re2.md) *, der am*Anfang und/oder am Ende des Texts abgeschnitten werden soll.  
* *Text*: eine Zeichenfolge.

## <a name="returns"></a>Gibt zurück

*Text* nach dem Kürzen der Übereinstimmungen von *Regex* , die am Anfang und/oder am *Ende des Texts*gefunden wurden.

## <a name="example"></a>Beispiel

Die *Teil Zeichenfolge* der Anweisungs untergeordneten Knoten vom Start und vom Ende der *string_to_trim*:

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


 