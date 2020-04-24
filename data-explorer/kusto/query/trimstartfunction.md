---
title: trim_start() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird trim_start() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d4ae71f73e76005f89766d974192c8eb24cd74d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505575"
---
# <a name="trim_start"></a>trim_start()

Entfernt die führende Übereinstimmung des angegebenen regulären Ausdrucks.

**Syntax**

`trim_start(`*regex-Text* `,` *text*`)`

**Argumente**

* *regex*: Zeichenfolge oder [regulärer Ausdruck,](re2.md) der vom Anfang des *Textes*abgeschnitten werden soll.  
* *text*: Eine Zeichenfolge.

**Rückgabe**

*Text* nach dem Trimmen der Übereinstimmung von *regex* am Anfang des *Textes*gefunden .

**Beispiel**

Statement-Balg-Trimmung *ab* *Anfang string_to_trim*:

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

Die nächste Anweisung schneidet alle Nicht-Wort-Zeichen vom Anfang der Zeichenfolge:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- Te st1/|Te st1/|
|- Te st2/|Te st2/|
|- Te st3/|Te st3/|
|- Te st4/|Te st4/|
|- Te st5/|Te st5/|

 