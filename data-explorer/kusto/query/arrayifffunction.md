---
title: array_iif() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird array_iif() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: f99b9aa8a9d081a7f28cd2e5bb8750b15f2fcdac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663911"
---
# <a name="array_iif"></a>array_iif()

Elementweise iif-Funktion auf dynamischen Arrays.

Ein weiterer Alias: array_iff().

**Syntax**

`array_iif(`*ConditionArray*, *ifTrue*, *IfFalse*]`)`

**Argumente**

* *conditionArray*: Eingabearray von *booleschen* oder numerischen Werten, muss dynamisches Array sein.
* *ifTrue*: Eingabearray von Werten oder primitiven Wert - der Ergebniswert(n), wenn der entsprechende Wert von *ConditionArray* *true*ist.
* *ifFalse*: Eingabearray von Werten oder primitiven Wert - der Ergebniswert(n), wenn der entsprechende Wert von *ConditionArray* *false*ist.

**Hinweise**

* Die Ergebnislänge ist die Länge von *conditionArray*.
* Numerischer Bedingungswert wird als *Bedingung* behandelt != *0*.
* Der nicht numerische/NULL-Bedingungswert hat null im entsprechenden Index des Ergebnisses.
* Fehlende Werte (in Arrays mit kürzerer Länge) werden als NULL behandelt.

**Rückgabe**

Dynamisches Array der Werte, die entweder aus den *IfTrue-* oder *IfFalse* [array]-Werten gemäß dem entsprechenden Wert des Condition-Arrays entnommen wurden.

**Beispiel**

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|[wahr, falsch, wahr]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
