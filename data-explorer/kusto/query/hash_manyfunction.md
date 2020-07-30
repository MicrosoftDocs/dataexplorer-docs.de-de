---
title: hash_many ()-Azure Daten-Explorer
description: In diesem Artikel wird hash_many () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 3d0f389264d078d2b55ac06214bb3b820fcf7f13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347594"
---
# <a name="hash_many"></a>hash_many()

Gibt einen kombinierten Hashwert mit mehreren Werten zurück.

## <a name="syntax"></a>Syntax

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

## <a name="arguments"></a>Argumente

* *S1*, *S2*,..., *SN*: Eingabewerte, für die ein Hashwert erstellt wird.

## <a name="returns"></a>Gibt zurück

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
