---
title: bin ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird bin () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6fc2e55b43e7c7c2dc2bb537730f8f627e3e4a66
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513113"
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
* *roundTo*: die "bin size". Eine Zahl oder ein TimeSpan- *Wert*, der den Wert dividiert 

## <a name="returns"></a>Gibt zurück

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
