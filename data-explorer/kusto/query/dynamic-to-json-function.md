---
title: dynamic_to_json ()-Azure Daten-Explorer
description: In diesem Artikel wird dynamic_to_json () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 010610eb8cf6727c752f04564e8b3d1f71a405ab
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502152"
---
# <a name="dynamic_to_json"></a>dynamic_to_json ()

Konvertiert `dynamic` Eingaben in eine Zeichen folgen Darstellung.
Wenn die Eingabe ein Eigenschaften Behälter ist, druckt die Ausgabe Zeichenfolge ihren Inhalt rekursiv sortiert nach den Schlüsseln. Andernfalls ähnelt die Ausgabe der `tostring` funktionsausgabe.

## <a name="syntax"></a>Syntax

`dynamic_to_json(Expr)`

## <a name="arguments"></a>Argumente

* *Expr*: `dynamic` Input. Die Funktion akzeptiert ein Argument.

## <a name="returns"></a>Rückgabe

Gibt eine Zeichen folgen Darstellung der `dynamic` Eingabe zurück. Wenn die Eingabe ein Eigenschaften Behälter ist, druckt die Ausgabe Zeichenfolge ihren Inhalt rekursiv sortiert nach den Schlüsseln.

## <a name="examples"></a>Beispiele

Ausdruck:

  Let bag1 = dynamic_to_json (Dynamic ({' Y10 ':d YNAMIC ({}), ' X8 ': Dynamic ({' C3 ': 1, t 8 ': 5, ' A4 ': 6}), d ': 114, ' a1 ': 12, ' B1 ': 2, ' C1 ': 3, ' A14 ': [15, 13, 18]})); Druck bag1
  
Ergebnis:

"{" "A1" ": 12," "A14" ": [15, 13, 18]," "B1" ": 2," "C1" ": 3," "D1" ": 114," "X8" ": {" "C3" ": 1," "D8" ": 5," "A4" ": 6}," "Y10" ": {} }"

Ausdruck:

 Let BAG2 = dynamic_to_json (Dynamic ({' X8 ': Dynamic ({' A4 ': 6, ' C3 ': 1, t 8 ': 5}), ' A14 ': [15, 13, 18], ' C1 ': 3, ' B1 ': 2, ' Y10 ': Dynamic ({}), ' a1 ': 12, 'd 1 ': 114})); Druck BAG2
 
Ergebnis:

{"A1": 12, "A14": [15, 13, 18], "B1": 2, "C1": 3, "D1": 114, "X8": {"A4": 6, "C3": 1, "D8": 5}, "Y10": {} }
