---
title: array_iif ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird array_iif () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: 7f11e0c00b19fd24ed2db326f3209236ecf25ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246891"
---
# <a name="array_iif"></a>array_iif()

Element Weise IIf-Funktion für dynamische Arrays.

Ein anderer Alias: array_iff ().

## <a name="syntax"></a>Syntax

`array_iif(`*ConditionArray*, *ifTrue*, *IfFalse*]`)`

## <a name="arguments"></a>Argumente

* *ConditionArray*: ein Eingabe Array von *booleschen* oder numerischen Werten muss ein dynamisches Array sein.
* *ifTrue*: Eingabe Array von Werten oder primitiver Wert-der Ergebniswert (e), wenn der entsprechende Wert von *ConditionArray* *true*ist.
* *IfFalse*: Eingabe Array von Werten oder primitiver Wert-der Ergebniswert (e), wenn der entsprechende Wert von *ConditionArray* *false*ist.

**Notizen**

* Die Ergebnis Länge ist die Länge von *ConditionArray*.
* Der Wert der numerischen Bedingung wird als *Bedingung* behandelt! = *0*.
* Ein nicht numerischer/NULL-Bedingungs Wert weist im entsprechenden Index des Ergebnisses den Wert NULL auf.
* Fehlende Werte (bei Arrays mit kürzerer Länge) werden als NULL behandelt.

## <a name="returns"></a>Rückgabe

Dynamisches Array der Werte, die entweder aus den Werten von *ifTrue* oder *IfFalse* [Array] entnommen werden, entsprechend dem entsprechenden Wert des Bedingungs Arrays.

## <a name="example"></a>Beispiel

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|[true, false, true]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
