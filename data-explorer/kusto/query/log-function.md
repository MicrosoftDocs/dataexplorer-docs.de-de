---
title: log() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt log() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7bd3c4f5c6b262587cab2327486945f5d78aaf02
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513276"
---
# <a name="log"></a>log()

`log()`gibt die natürliche Logarithmusfunktion zurück.  

**Syntax**

`log(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl > 0.

**Rückgabe**

* Der natürliche Logarithmus ist der Basis-e-Logarithmus: die Umkehrung der natürlichen exponentiellen Funktion (exp).
* `null`wenn das Argument negativ oder null ist oder `real` nicht in einen Wert konvertiert werden kann. 

**Siehe auch**

* Häufige (Basis-10) Logarithmen finden Sie unter [log10()](log10-function.md).
* Basis-2-Logarithmen finden Sie unter [log2()](log2-function.md)