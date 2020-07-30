---
title: Bereich ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird der Bereich () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2606746e89d645601fa53ed7f81d67ddae203c03
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345911"
---
# <a name="range"></a>range()

Generiert ein dynamisches Array, das eine Reihe gleichmäßiger Werte enthält.

## <a name="syntax"></a>Syntax

`range(`*starten* `,` Sie *stop*[ `,` *Schritt*] abbrechen`)` 

## <a name="arguments"></a>Argumente

* *Start*: der Wert des ersten Elements im resultierenden Array. 
* *Beenden*: der Wert des letzten Elements im resultierenden Array oder der kleinste Wert, der größer als das letzte Element im resultierenden Array und innerhalb eines ganzzahligen Vielfachen von *Step* from *Start*ist.
* *Step*: der Unterschied zwischen zwei aufeinander folgenden Elementen des Arrays. Der Standardwert für *Step* ist `1` für numeric und `1h` für `timespan` oder.`datetime`

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
