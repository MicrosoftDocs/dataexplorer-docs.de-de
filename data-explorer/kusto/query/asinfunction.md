---
title: asin() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt asin() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5db112daeba59dd841b02df8ba1a41185654ad6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518546"
---
# <a name="asin"></a>asin()

Gibt den Winkel zurück, dessen Sinus die [`sin()`](sinfunction.md)angegebene Zahl ist (der inverse Vorgang von ) .

**Syntax**

`asin(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl im Bereich [-1, 1].

**Rückgabe**

* Der Wert des Bogensinus von`x`
* `null`Wenn `x` < -1 oder `x` > 1