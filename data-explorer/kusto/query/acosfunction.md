---
title: acos ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel werden acos () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 486be5487071fa281765de410fe4e5be1ce62a38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253117"
---
# <a name="acos"></a>acos()

Gibt den Winkel zurück, dessen Kosinus die angegebene Zahl ist (umgekehrter Vorgang von [`cos()`](cosfunction.md) ).

## <a name="syntax"></a>Syntax

`acos(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl im Bereich [-1, 1].

## <a name="returns"></a>Rückgabe

* Der Wert für den Arkus Kosinus von. `x`
* `null` Wenn `x` <-1 oder `x` > 1