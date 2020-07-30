---
title: jetzt ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird jetzt () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9beae6edd1361715dfe84f851ca0a9bb69f4299c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346574"
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