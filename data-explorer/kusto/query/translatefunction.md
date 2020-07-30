---
title: Translation ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird übersetzt () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: d0e99048f3f1b0e3ce5c6c59a65ea645b22d15fe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340053"
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

## <a name="returns"></a>Gibt zurück

*Text* nach dem Ersetzen aller ocurrences of-Zeichen in "REPLACEMENTLIST" durch die entsprechenden Zeichen in "SEARCHLIST"

## <a name="examples"></a>Beispiele

|Eingabe                                 |Output   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|