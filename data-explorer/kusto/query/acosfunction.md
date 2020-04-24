---
title: acos() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt acos() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: aa33714d57b319ba5a775385e8ee7d232addfe9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519328"
---
# <a name="acos"></a>acos()

Gibt den Winkel zurück, dessen Cosinus die [`cos()`](cosfunction.md)angegebene Zahl ist (der inverse Vorgang von ) .

**Syntax**

`acos(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl im Bereich [-1, 1].

**Rückgabe**

* Der Wert des Bogenkosinus von`x`
* `null`Wenn `x` < -1 oder `x` > 1