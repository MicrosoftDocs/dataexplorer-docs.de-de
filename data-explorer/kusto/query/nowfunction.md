---
title: jetzt ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird jetzt () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 63b99b4774c46872b83e526ca00a5d974f0b0c19
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249810"
---
# <a name="now"></a>now()

Gibt die aktuelle UTC-Uhrzeit (optional) um einen bestimmten TimeSpan-Wert zurück.
Diese Funktion kann mehrmals in einer Anweisung verwendet werden, und die Uhrzeit, auf die verwiesen wird, ist für alle Instanzen identisch.

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>Syntax

`now(`[*Offset*]`)`

## <a name="arguments"></a>Argumente

* *Offset*: eine `timespan` , die zur aktuellen UTC-Uhrzeit hinzugefügt wurde. Standardwert: 0.

## <a name="returns"></a>Rückgabe

Die aktuelle UTC-Uhrzeit als `datetime`-Wert.

`now()` + *kompensieren* 

## <a name="example"></a>Beispiel

Bestimmt das Intervall seit dem vom Prädikat identifizierten Ereignis:

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```