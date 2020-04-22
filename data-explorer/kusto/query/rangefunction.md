---
title: bereich() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt bereich() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86558591e6312edd218230cda19a4afc17a13b27
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744587"
---
# <a name="range"></a>range()

Generiert ein dynamisches Array mit einer Reihe gleich verteilter Werte.

**Syntax**

`range(`*Startstopp* `,` *stop*`,` [ *Schritt*]`)` 

**Argumente**

* *start*: Der Wert des ersten Elements im resultierenden Array. 
* *stop*: Der Wert des letzten Elements im resultierenden Array oder der kleinste Wert, der größer als das letzte Element im resultierenden Array und innerhalb eines ganzzahligen Vielfachen von *step* from *start*ist.
* *schritt*: Die Differenz zwischen zwei aufeinander folgenden Elementen des Arrays. Der Standardwert *step* für `1` Schritt `1h` ist `timespan` für numerische und für oder`datetime`

**Beispiele**

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
