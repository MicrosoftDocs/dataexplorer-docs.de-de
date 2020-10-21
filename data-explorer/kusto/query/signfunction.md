---
title: Sign ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird Sign () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: db6757e8c3ca871036cba1c21c1a20c3486b9249
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250002"
---
# <a name="sign"></a>sign()

Vorzeichen eines numerischen Ausdrucks

## <a name="syntax"></a>Syntax

`sign(`*Stuben*`)`

## <a name="arguments"></a>Argumente

* *x*: eine reelle Zahl.

## <a name="returns"></a>Rückgabe

* Das positive (+ 1), NULL (0) oder negative Vorzeichen (-1) des angegebenen Ausdrucks. 

## <a name="examples"></a>Beispiele

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|