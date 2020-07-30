---
title: 'GetSchema-Operator: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den GetSchema-Operator in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4fe19148ef8f410f04dc68f435734a2c2c425cca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347679"
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
|Sichten|3|System.Int64|long
|Byteszugestellt|4|System.Int64|long
