---
title: trim() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird trim() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aef2a61e5ac13fe9af9d8bc0dd130f3d085a3604
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505592"
---
# <a name="trim"></a>trim()

Entfernt alle führenden und nachgestellten Übereinstimmungen des angegebenen regulären Ausdrucks.

**Syntax**

`trim(`*regex-Text* `,` *text*`)`

**Argumente**

* *regex*: Zeichenfolge oder [regulärer Ausdruck,](re2.md) der vom Anfang und/oder am Ende des *Textes*getrimmt werden soll.  
* *text*: Eine Zeichenfolge.

**Rückgabe**

*Text* nach dem Trimmen von Übereinstimmungen von *Regex,* die am Anfang und/oder am Ende des *Textes*gefunden wurden.

**Beispiel**

Statement-Balg-Trimmung *steilstring* vom Anfang und Ende des *string_to_trim*:

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

Die nächste Anweisung schneidet alle Nicht-Wort-Zeichen vom Anfang und Ende der Zeichenfolge:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- Te st1/|Te st1|
|- Te st2/|Te st2|
|- Te st3/|Te st3|
|- Te st4/|Te st4|
|- Te st5/|Te st5|


 