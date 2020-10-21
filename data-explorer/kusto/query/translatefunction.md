---
title: Translation ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird übersetzt () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: 5d186172a0be1780347dbf89600c200c00687291
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252038"
---
# <a name="translate"></a>translate()

Ersetzt einen Zeichensatz ("SEARCHLIST") durch einen anderen Zeichensatz ("REPLACEMENTLIST") in einer angegebenen Zeichenfolge.
Die-Funktion sucht in "SEARCHLIST" nach Zeichen und ersetzt diese durch die entsprechenden Zeichen in "REPLACEMENTLIST".

## <a name="syntax"></a>Syntax

`translate(`*SEARCHLIST* `,` *Ersatz Liste* `,` *Text*`)`

## <a name="arguments"></a>Argumente

* *SEARCHLIST*: die Liste der Zeichen, die ersetzt werden sollen.
* *REPLACEMENTLIST*: die Liste der Zeichen, die die Zeichen in "SEARCHLIST" ersetzen sollen.
* *Text*: eine zu durchsuchende Zeichenfolge

## <a name="returns"></a>Rückgabe

*Text* nach dem Ersetzen aller ocurrences of-Zeichen in "REPLACEMENTLIST" durch die entsprechenden Zeichen in "SEARCHLIST"

## <a name="examples"></a>Beispiele

|Eingabe                                 |Output   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|