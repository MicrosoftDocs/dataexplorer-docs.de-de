---
title: 'Verzweigungs Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird der Fork-Operator in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c20cb3311a254f0154efa01c9d155cd6352c6ef
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356553"
---
# <a name="fork-operator"></a>fork-Operator

Führt mehrere consumeroperatoren parallel aus.

## <a name="syntax"></a>Syntax

*T* `|` `fork` [*Name* `=` ] `(` *unter* Abfrage `)` [*Name* `=` ] `(` *Unterabfrage* `)` ...

## <a name="arguments"></a>Argumente

* *Unterabfrage* ist eine Downstream-Pipeline von Abfrage Operatoren.
* *Name* ist ein temporärer Name für die Ergebnistabelle der Unterabfrage.

## <a name="returns"></a>Rückgabe

Mehrere Ergebnistabellen, eine für jede der Unterabfragen.

**Unterstützte Operatoren**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`project-keep`](project-keep-operator.md), [`project-rename`](projectrenameoperator.md), [`project-reorder`](projectreorderoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**Notizen**

* [`materialize`](materializefunction.md) die Funktion kann als Ersatz für die Verwendung von [`join`](joinoperator.md) oder [`union`](unionoperator.md) auf Fork-Beinen verwendet werden.
Der Eingabestream wird von materialisieren zwischengespeichert, und anschließend kann der zwischengespeicherte Ausdruck in Join/Union-Beinen verwendet werden.

* Ein Name, der vom- `name` Argument oder mithilfe des- [`as`](asoperator.md) Operators angegeben wird, wird als verwendet, um die Ergebnisregister Karte im Tool zu benennen [`Kusto.Explorer`](../tools/kusto-explorer.md) .

* Vermeiden Sie die Verwendung `fork` von mit einer einzelnen Unterabfrage.

* Verwenden Sie [Batch](batches.md) mit [`materialize`](materializefunction.md) Tabellen Ausdrucks Anweisungen für den- `fork` Operator.

## <a name="examples"></a>Beispiele

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