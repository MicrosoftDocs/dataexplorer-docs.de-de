---
title: log10() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird log10() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 9ccc83ff466d0414f793b7cfbbcf10d2ca169348
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513191"
---
# <a name="log10"></a>log10()

`log10()`gibt die gemeinsame Logarithmusfunktion (Base-10) zurück.  

**Syntax**

`log10(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl > 0.

**Rückgabe**

* Der gemeinsame Logarithmus ist der Basis-10-Logarithmus: die Umkehrung der exponentiellen Funktion (exp) mit Basis 10.
* `null`wenn das Argument negativ oder null ist oder `real` nicht in einen Wert konvertiert werden kann. 

**Siehe auch**

* Für natürliche (Base-e) Logarithmen siehe [log()](log-function.md).
* Basis-2-Logarithmen finden Sie unter [log2()](log2-function.md)