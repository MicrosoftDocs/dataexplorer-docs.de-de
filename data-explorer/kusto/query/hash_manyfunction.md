---
title: hash_many ()-Azure Daten-Explorer
description: In diesem Artikel wird hash_many () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a323491a2d3c4e78684c8bcaff6de8c55573d61a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243298"
---
# <a name="hash_many"></a>hash_many()

Gibt einen kombinierten Hashwert mit mehreren Werten zurück.

## <a name="syntax"></a>Syntax

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

## <a name="arguments"></a>Argumente

* *S1*, *S2*,..., *SN*: Eingabewerte, für die ein Hashwert erstellt wird.

## <a name="returns"></a>Rückgabe

Der kombinierte Hashwert der angegebenen skalare.

## <a name="examples"></a>Beispiele

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|erter|
|---|---|---|
|Hallo|World|-1440138333540407281|
