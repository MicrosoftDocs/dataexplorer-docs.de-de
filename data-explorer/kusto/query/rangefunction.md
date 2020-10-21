---
title: Bereich ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird der Bereich () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a37d375ca83252b063821659f0b5490337c6667
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241045"
---
# <a name="range"></a>range()

Generiert ein dynamisches Array, das eine Reihe gleichmäßiger Werte enthält.

## <a name="syntax"></a>Syntax

`range(`*starten* `,` Sie *stop*[ `,` *Schritt*] abbrechen`)` 

## <a name="arguments"></a>Argumente

* *Start*: der Wert des ersten Elements im resultierenden Array. 
* *Beenden*: der Wert des letzten Elements im resultierenden Array oder der kleinste Wert, der größer als das letzte Element im resultierenden Array und innerhalb eines ganzzahligen Vielfachen von *Step* from *Start*ist.
* *Step*: der Unterschied zwischen zwei aufeinander folgenden Elementen des Arrays. Der Standardwert für *Step* ist `1` für numeric und `1h` für `timespan` oder. `datetime`

## <a name="examples"></a>Beispiele

Das folgende Beispiel gibt `[1, 4, 7]`zurück:

```kusto
T | extend r = range(1, 8, 3)
```

Das folgende Beispiel gibt ein Array mit allen Tagen im Jahr 2015 zurück:

```kusto
T | extend r = range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

Das folgende Beispiel gibt `[1,2,3]`zurück:

```kusto
range(1, 3)
```

Das folgende Beispiel gibt `["01:00:00","02:00:00","03:00:00","04:00:00","05:00:00"]`zurück:

```kusto
range(1h, 5h)
```
