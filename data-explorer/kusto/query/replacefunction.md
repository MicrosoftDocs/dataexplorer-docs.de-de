---
title: ersetzen() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird replace() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84a741f10172ef418da7d92b8c1ad6ba26593d72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510335"
---
# <a name="replace"></a>replace()

Ersetzen Sie alle regex-Übereinstimmungen mit einer anderen Zeichenfolge.

**Syntax**

`replace(`*regex* `,` *Text neu schreiben* `,` *text*`)`

**Argumente**

* *regex*: Der [reguläre Ausdruck](https://github.com/google/re2/wiki/Syntax) zum Durchsuchen von *Text*. Er kann Erfassungsgruppen in „('Klammern')“ enthalten. 
* *rewrite*: Der Ersatzregex für jede Übereinstimmung, die durch *übereinstimmendeRegex*gemacht wird. Verwenden Sie `\0`, um auf die gesamte Übereinstimmung zu verweisen: `\1` für die erste Erfassungsgruppe, `\2` usw. für nachfolgende Erfassungsgruppen.
* *text*: Eine Zeichenfolge.

**Rückgabe**

*text* nach dem Ersetzen aller Übereinstimmungen von *regex* durch Auswertungen von *rewrite*. Übereinstimmungen überlappen sich nicht.

**Beispiel**

Diese Anweisung:

```kusto
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Bietet die folgenden Ergebnisse:

| x    | str | replaced|
|---|---|---|
| 1    | Nummer lautet 1.000000  | Nummer lautete 1.000000|
| 2    | Nummer lautet 2.000000  | Nummer lautete 2.000000|
| 3    | Nummer lautet 3.000000  | Nummer lautete 3.000000|
| 4    | Nummer lautet 4.000000  | Nummer lautete 4.000000|
| 5    | Nummer lautet 5.000000  | Nummer lautete 5.000000|
 