---
title: log2() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird log2() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 41e9a1457f97fa04a4daa54e1929f27d8a448ae3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513123"
---
# <a name="log2"></a>log2()

`log2()`gibt die Base-2-Logarithmusfunktion zurück.  

**Syntax**

`log2(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl > 0.

**Rückgabe**

* Der Logarithmus ist der Base-2-Logarithmus: die Umkehrung der exponentiellen Funktion (exp) mit Basis 2.
* `null`wenn das Argument negativ oder null ist oder `real` nicht in einen Wert konvertiert werden kann. 

**Siehe auch**

* Für natürliche (Base-e) Logarithmen siehe [log()](log-function.md).
* Häufige (Basis-10) Logarithmen finden Sie unter [log10()](log10-function.md).