---
title: now() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird jetzt() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c1a130cfbd45c35ff1ba26ed6c47986fc186c89c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512052"
---
# <a name="now"></a>now()

Gibt die aktuelle UTC-Uhrzeit zurück, die optional durch eine bestimmte Zeitspanne ausgeglichen wird.
Diese Funktion kann mehrmals in einer Anweisung verwendet werden, und die Uhrzeit, auf die verwiesen wird, ist für alle Instanzen identisch.

```kusto
now()
now(-2d)
```

**Syntax**

`now(`[*Offset*]`)`

**Argumente**

* *offset*: `timespan`A , der aktuellen UTC-Uhrzeit hinzugefügt. Standardwert: 0.

**Rückgabe**

Die aktuelle UTC-Uhrzeit als `datetime`-Wert.

`now()` + *Offset* 

**Beispiel**

Bestimmt das Intervall seit dem vom Prädikat identifizierten Ereignis:

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```