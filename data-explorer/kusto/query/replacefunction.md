---
title: Replace ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: Dieser Artikel beschreibt Replace () in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d9fdcfaad41201cd99796c3f4966593aa7480e49
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243063"
---
# <a name="replace"></a>replace()

Ersetzen Sie alle regex-Übereinstimmungen mit einer anderen Zeichenfolge.

## <a name="syntax"></a>Syntax

`replace(`*Regex* `,` neu *Schreiben* `,` *Text*`)`

## <a name="arguments"></a>Argumente

* *Regex*: der [reguläre Ausdruck](https://github.com/google/re2/wiki/Syntax) zum Durchsuchen von *Text*. Er kann Erfassungsgruppen in „('Klammern')“ enthalten. 
* *Rewrite*: der Ersetzungs-Regex für jede von *matchingregex*vorgenommene Übereinstimmung. Verwenden Sie `\0`, um auf die gesamte Übereinstimmung zu verweisen: `\1` für die erste Erfassungsgruppe, `\2` usw. für nachfolgende Erfassungsgruppen.
* *Text*: eine Zeichenfolge.

## <a name="returns"></a>Rückgabe

*text* nach dem Ersetzen aller Übereinstimmungen von *regex* durch Auswertungen von *rewrite*. Übereinstimmungen überlappen sich nicht.

## <a name="example"></a>Beispiel

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
 