---
title: AVG () (Aggregations Funktion)-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird AVG () (Aggregations Funktion) in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: f058af075a856d12a2a6a81419f32b6efbd9ea16
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349396"
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
 