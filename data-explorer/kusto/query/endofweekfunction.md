---
title: Endofweek() - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt Endofweek() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83c080c60e34dbfdf19f7dde870621e34ded836d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515826"
---
# <a name="endofweek"></a>endofweek()

Gibt das Ende der Woche zurück, in der das Datum enthalten ist, verschoben um einen Offset, sofern angegeben.

Der letzte Tag der Woche gilt als Samstag.

**Syntax**

`endofweek(`*Datum* `,`[*Offset*]`)`

**Argumente**

* `date`: Das Eingabedatum.
* `offset`: Eine optionale Anzahl von Offsetwochen ab dem Eingabedatum (ganzzahlig, default - 0).

**Rückgabe**

Eine Datumszeit, die das Ende der Woche für den angegebenen *Datumswert* darstellt, mit dem Offset, falls angegeben.

**Beispiel**

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|Wochenende|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-07 23:59:59.9999999|
|2017-01-14 23:59:59.9999999|