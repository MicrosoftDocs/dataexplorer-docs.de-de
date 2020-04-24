---
title: getschema-Operator - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den getschema-Operator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2faeee575f1af72cfad808253853ae96aba7a28f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514364"
---
# <a name="getschema-operator"></a>getschema-Operator 

Erstellen Sie eine Tabelle, die ein tabellenförmiges Schema der Eingabe darstellt.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**Syntax**

*T* `| ``getschema`

**Beispiel**

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
|BytesDelivered|4|System.Int64|long