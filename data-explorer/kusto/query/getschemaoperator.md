---
title: 'GetSchema-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den GetSchema-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6960a737b0a71a6b921a6a58750e48f5c3fb9da0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247381"
---
# <a name="getschema-operator"></a>getschema-Operator 

Erzeugt eine Tabelle, die ein tabellarisches Schema der Eingabe darstellt.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

## <a name="syntax"></a>Syntax

*T* `| ``getschema`

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|ColumnType|
|---|---|---|---|
|Timestamp|0|System.DateTime|datetime|
|Sprache|1|System.String|Zeichenfolge|
|Seite|2|System.String|Zeichenfolge|
|Ansichten|3|System.Int64|long
|Byteszugestellt|4|System.Int64|long
