---
title: bin() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt bin() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fb827c71fa63fde031a91bc9aec7f0ed108fd5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517424"
---
# <a name="bin"></a>bin()

Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist. 

Häufig in Kombination [`summarize by ...`](./summarizeoperator.md)mit verwendet.
Wenn Sie über einen verstreuten Satz von Werten verfügen, werden sie zu einem kleineren Satz bestimmter Werte gruppiert.

Nullwerte, eine Null-Lagerplatzgröße oder eine negative Lagerplatzgröße führen zu NULL. 

Alias `floor()` zu funktionieren.

**Syntax**

`bin(`*Wert*`,`*roundTo*`)`

**Argumente**

* *Wert*: Eine Zahl, ein Datum oder eine Zeitspanne. 
* *roundTo*: Die "bin-Größe". Eine Zahl, ein Datum oder ein Zeitraum zum Teilen des Werts ( *value*). 

**Rückgabe**

Das nächste Vielfache von *roundTo* unter dem Wert (*value*).  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

**Beispiele**

Ausdruck | Ergebnis
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


Der folgende Ausdruck berechnet ein Histogramm der Dauer mit einer Bucketgröße von 1 Sekunde:

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```