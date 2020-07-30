---
title: bin ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird bin () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 434d32a3b6597d71ea22c182a468d64d7971e6cb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348971"
---
# <a name="bin"></a>bin()

Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist. 

Wird häufig in Kombination mit verwendet [`summarize by ...`](./summarizeoperator.md) .
Wenn Sie über einen verstreuten Satz von Werten verfügen, werden sie zu einem kleineren Satz bestimmter Werte gruppiert.

NULL-Werte, eine NULL-bin-Größe oder eine negative bin-Größe ergeben NULL. 

Alias für die `floor()` Funktion.

## <a name="syntax"></a>Syntax

`bin(`*Wert* `,` *roundTo*`)`

## <a name="arguments"></a>Argumente

* *Wert*: eine Zahl, ein Datum oder ein TimeSpan-Wert. 
* *roundTo*: die "bin size". Eine Zahl, ein Datum oder ein Zeitraum zum Teilen des Werts ( *value*). 

## <a name="returns"></a>Rückgabe

Das nächste Vielfache von *roundTo* unter dem Wert (*value*).  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>Beispiele

expression | Ergebnis
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


Der folgende Ausdruck berechnet ein Histogramm der Dauer mit einer Bucketgröße von 1 Sekunde:

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```