---
title: 'count () (Aggregations Funktion): Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Funktion "count ()" (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5b69b50c0cf4c07934d7900937675bf6338eab9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348767"
---
# <a name="countif-aggregation-function"></a>count () (Aggregations Funktion)

Gibt die Anzahl von Zeilen zurück, für die *Predicate* als `true` ausgewertet wird.

* Kann [nur im Kontext der Aggregation innerhalb von](summarizeoperator.md) Zusammenfassung verwendet werden.

Siehe auch- [count ()](count-aggfunction.md) -Funktion, die Zeilen ohne Prädikat Ausdruck zählt.

## <a name="syntax"></a>Syntax

`countif(` *Prädikat* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Predicate*: ein Ausdruck, der für die Aggregations Berechnung verwendet wird. 

## <a name="returns"></a>Rückgabe

Gibt die Anzahl von Zeilen zurück, für die *Predicate* als `true` ausgewertet wird.

> [!TIP]
> Verwenden von `summarize countif(filter)` anstelle von `where filter | summarize count()`