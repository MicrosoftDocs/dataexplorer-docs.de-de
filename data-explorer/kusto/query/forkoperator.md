---
title: Gabeloperator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Gabeloperator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 13cd837b3874a55ec758991f5609e089daba7c75
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515197"
---
# <a name="fork-operator"></a>fork-Operator

Führt mehrere Consumeroperatoren parallel aus.

**Syntax**

*T* `|` `=``(``)` `=``(`*name*`)` *name**subquery* *subquery* [ name ] Unterabfrage [ Name ] Unterabfrage ... `fork`

**Argumente**

* *Unterabfrage* ist eine nachgelagerte Pipeline von Abfrageoperatoren
* *Name* ist ein temporärer Name für die Unterabfrageergebnistabelle

**Rückgabe**

Mehrere Ergebnistabellen, eine für jede der Unterabfragen.

**Unterstützte Operatoren**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**Hinweise**

* [`materialize`](materializefunction.md)Funktion kann als Ersatz für [`join`](joinoperator.md) [`union`](unionoperator.md) die Verwendung oder auf Gabelbeinen verwendet werden.
Der Eingabestream wird durch materialize zwischengespeichert, und dann kann der zwischengespeicherte Ausdruck in Join/Union-Beinen verwendet werden.

* Ein Name, der `name` durch das [`as`](asoperator.md) Argument oder durch die Verwendung des [`Kusto.Explorer`](../tools/kusto-explorer.md) Operators angegeben wird, wird als der verwendet, um die Ergebnisregisterkarte im Werkzeug zu benennen.

* Vermeiden `fork` Sie die Verwendung mit einer einzelnen Unterabfrage.

* Bevorzugen Sie [`materialize`](materializefunction.md) die Verwendung von `fork` [Batch](batches.md) mit tabellarischen Ausdrucksanweisungen über den Operator.

**Beispiele**

```kusto
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 )
    ( project Timestamp, EventText | top 1000 by Timestamp desc)
    ( summarize min(Timestamp), max(Timestamp) by ActivityID )
 
// In the following examples the result tables will be named: Errors, EventsTexts and TimeRangePerActivityID
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 | as Errors )
    ( project Timestamp, EventText | top 1000 by Timestamp desc | as EventsTexts )
    ( summarize min(Timestamp), max(Timestamp) by ActivityID | as TimeRangePerActivityID )
    
 KustoLogs
| where Timestamp > ago(1h)
| fork
    Errors = ( where Level == "Error" | project EventText | limit 100 )
    EventsTexts = ( project Timestamp, EventText | top 1000 by Timestamp desc )
    TimeRangePerActivityID = ( summarize min(Timestamp), max(Timestamp) by ActivityID )
```