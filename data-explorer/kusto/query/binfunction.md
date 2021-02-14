---
title: 'bin(): Azure Data Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt bin() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: edca199ed2ba12fa74e69c66f1b1396313606256
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359895"
---
# <a name="bin"></a>bin()

Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist. 

Wird häufig in Kombination mit [`summarize by ...`](./summarizeoperator.md) verwendet.
Wenn Sie über einen verstreuten Satz von Werten verfügen, werden sie zu einem kleineren Satz bestimmter Werte gruppiert.

NULL-Werte, eine bin-Größe von NULL oder eine negative bin-Größe ergeben NULL. 

Alias für `floor()`-Funktion.

## <a name="syntax"></a>Syntax

`bin(`*value*`,`*roundTo*`)`

## <a name="arguments"></a>Argumente

* *value:* Eine Zahl, ein Datum oder ein [Zeitraum](scalar-data-types/timespan.md) 
* *roundTo*: Die Bingröße. Eine Zahl oder ein Zeitraum zum Teilen des Werts ( *value*). 

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
