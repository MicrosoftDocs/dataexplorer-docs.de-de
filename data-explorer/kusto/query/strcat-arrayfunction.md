---
title: strcat_array ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird strcat_array () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b8369d2e994477c9d01880632fac5f8a3ebaf6a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342596"
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