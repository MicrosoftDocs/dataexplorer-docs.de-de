---
title: strcat_array ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird strcat_array () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06bdf7eb55b61cb974e803c9cdeb7b37b34ad87e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243790"
---
# <a name="strcat_array"></a>strcat_array()

Erstellt mithilfe des angegebenen Trenn Zeichens eine verketteten Zeichenfolge von Array Werten.
    
## <a name="syntax"></a>Syntax

`strcat_array(`*Array*, *Trenn* Zeichen`)`

## <a name="arguments"></a>Argumente

* *Array*: ein `dynamic` Wert, der ein Array von zu verkettenden Werten darstellt.
* *Trennzeichen*: ein `string` Wert, der verwendet wird, um die Werte im *Array* zu verketten.

## <a name="returns"></a>Rückgabe

Array Werte, die zu einer einzelnen Zeichenfolge verkettet werden.

## <a name="examples"></a>Beispiele
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|