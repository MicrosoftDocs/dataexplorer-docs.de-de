---
title: sign() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt sign() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86a57ed2fb7d43daf300731fe48233eca318bded
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507564"
---
# <a name="sign"></a>sign()

Vorzeichen eines numerischen Ausdrucks

**Syntax**

`sign(`*X*`)`

**Argumente**

* *x*: Eine reelle Zahl.

**Rückgabe**

* Das positive (+1), Nullzeichen (0) oder negative (-1) Zeichen des angegebenen Ausdrucks. 

**Beispiele**

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|