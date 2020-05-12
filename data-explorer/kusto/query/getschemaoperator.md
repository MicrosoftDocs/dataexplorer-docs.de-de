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
ms.openlocfilehash: 333c4d59a7ed62fd031ab52019c10abd821fd858
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226753"
---
# <a name="getschema-operator"></a>getschema-Operator 

Erzeugt eine Tabelle, die ein tabellarisches Schema der Eingabe darstellt.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**Syntax**

*T* `| ``getschema`

**Beispiel**

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
