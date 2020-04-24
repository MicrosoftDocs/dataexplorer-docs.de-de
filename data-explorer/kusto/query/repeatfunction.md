---
title: repeat() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird Repeat() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fd944112a64e7400e38c627b7b0651bb6fccd54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510420"
---
# <a name="repeat"></a>repeat()

Generiert ein dynamisches Array mit einer Reihe gleicher Werte.

**Syntax**

`repeat(`*Wertanzahl* `,` *count*`)` 

**Argumente**

* *value*: Der Wert des Elements im resultierenden Array. Der *Werttyp* kann boolesch, ganzzahlig, lang, real, datetime oder timespan sein.   
* *count*: Die Anzahl der Elemente im resultierenden Array. Die *Anzahl* muss eine ganze Zahl sein.
Wenn *die Anzahl* gleich Null ist, wird ein leeres Array zurückgegeben.
Wenn *die Anzahl* kleiner als Null ist, wird ein NULL-Wert zurückgegeben. 

**Beispiele**

Das folgende Beispiel gibt `[1, 1, 1]`zurück:

```kusto
T | extend r = repeat(1, 3)
```