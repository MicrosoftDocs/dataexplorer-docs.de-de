---
title: AVG () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird AVG () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 949bd463f57b9eb0b674fe780aa50e78e30926a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248029"
---
# <a name="avg-aggregation-function"></a>AVG () (Aggregations Funktion)

Berechnet den Durchschnitt von *expr* in der Gruppe. 

* Kann nur im Kontext der [Aggregation in zusammen](summarizeoperator.md) Fassung verwendet werden.

## <a name="syntax"></a>Syntax

`avg(` *expr* zusammenfassen`)`

## <a name="arguments"></a>Argumente

* *Expr*: Ausdruck, der für die Aggregations Berechnung verwendet wird. Datensätze mit `null` Werten werden ignoriert und nicht in die Berechnung eingeschlossen.

## <a name="returns"></a>Rückgabe

Der Durchschnittswert von *expr* in der Gruppe.
 