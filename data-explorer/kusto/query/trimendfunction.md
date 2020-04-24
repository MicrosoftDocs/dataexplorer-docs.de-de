---
title: trim_end() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird trim_end() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a6f6ffc264cb436fc61d74f08dfded915caa05d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505643"
---
# <a name="trim_end"></a>trim_end()

Entfernt die nachfolgende Übereinstimmung des angegebenen regulären Ausdrucks.

**Syntax**

`trim_end(`*regex-Text* `,` *text*`)`

**Argumente**

* *regex*: Zeichenfolge oder [regulärer Ausdruck,](re2.md) der vom Ende des *Textes*abgeschnitten werden soll.  
* *text*: Eine Zeichenfolge.

**Rückgabe**

*Text* nach dem Zuschneiden von Übereinstimmungen von *Regex,* die am Ende des *Textes*gefunden wurden.

**Beispiel**

Statement-Balg-Trimmung *ab* *Ende string_to_trim*:

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing          |

Die nächste Anweisung schneidet alle Nicht-Wort-Zeichen vom Ende der Zeichenfolge:

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|- Te st1/|- Te st1  |
|- Te st2/|- Te st2  |
|- Te st3/|- Te st3  |
|- Te st4/|- Te st4  |
|- Te st5/|- Te st5  |