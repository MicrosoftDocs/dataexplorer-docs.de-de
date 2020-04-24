---
title: übersetzen() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Translate() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2019
ms.openlocfilehash: f128ce31af7fc720ee81b7471d3d74616197b8d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505711"
---
# <a name="translate"></a>translate()

Ersetzt einen Satz von Zeichen ('searchList') durch einen anderen Satz von Zeichen ('replacementList') in einer bestimmten Zeichenfolge.
Die Funktion sucht nach Zeichen in der 'searchList' und ersetzt sie durch die entsprechenden Zeichen in 'replacementList'

**Syntax**

`translate(`*searchList* `,` *replacementListentext* `,` *text*`)`

**Argumente**

* *searchList*: Die Liste der Zeichen, die ersetzt werden sollen
* *replacementList*: Die Liste der Zeichen, die die Zeichen in 'searchList' ersetzen sollen
* *Text*: Eine zu durchsuchende Zeichenfolge

**Rückgabe**

*Text* nach dem Ersetzen aller Zeichen in 'replacementList' durch die entsprechenden Zeichen in 'searchList'

**Beispiele**

|Eingabe                                 |Output   |
|--------------------------------------|---------|
|`translate("abc", "x", "abc")`        |`"xxx"`  |
|`translate("abc", "", "ab")`          |`""`     |
|`translate("krasp", "otsku", "spark")`|`"kusto"`|