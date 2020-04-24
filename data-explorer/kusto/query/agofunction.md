---
title: ago() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird ago() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d155f1a1cd113f73acc779e6c2e73d5f537e71c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519090"
---
# <a name="ago"></a>ago()

Subtrahiert den angegebenen Zeitraum von der aktuellen UTC-Uhrzeit.

```kusto
ago(1h)
ago(1d)
```

Wie `now()`kann diese Funktion mehrmals in einer Anweisung verwendet werden, und die UTC-Uhrzeit, auf die verwiesen wird, ist für alle Instanziierungen identisch.

**Syntax**

`ago(`*a_timespan*`)`

**Argumente**

* *a_timespan:* Intervall, das von der aktuellen UTC-Uhrzeit (`now()`) subtrahiert werden soll.

**Rückgabe**

`now() - a_timespan`

**Beispiel**

Alle Zeilen mit einem Zeitstempel der letzten Stunde:

```kusto
T | where Timestamp > ago(1h)
```