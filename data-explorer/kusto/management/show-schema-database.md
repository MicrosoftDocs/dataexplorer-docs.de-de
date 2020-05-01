---
title: '. Anzeigen des Datenbankschemas: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie das Datenbankschema in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90a48ec3a830e754bf823712ca7016d162474a39
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616982"
---
# <a name="show-databases-schema"></a>. Anzeigen des Datenbankschemas

Gibt eine flache Liste der Struktur der ausgewählten Datenbanken mit allen Tabellen und Spalten in einer einzelnen Tabelle oder einem JSON-Objekt zurück.
Bei Verwendung mit einer Version wird die Datenbank nur zurückgegeben, wenn Sie eine höhere Version als die bereitgestellte Version ist.

> [!NOTE]
> Die Version sollte nur im Format "vMM.mm" bereitgestellt werden. Mm stellt die Hauptversion und mm die neben Version dar.

`.show``database` *DatabaseName* `schema` [`details`] [`if_later_than` *"Version"*] 

`.show``database` *DatabaseName* `schema` [`if_later_than` *"Version"*] `as``json`
 
`.show``databases` `,` *DatabaseName1* DatabaseName1 `(` ... `)` `schema` [`details` | `as` `json`]
 
`.show``databases` *"Version"* `,` *DatabaseName1* DatabaseName1 if_later_than "Version"... `(` `)` `schema` [`details` | `as` `json`]

**Beispiel** 
 
Die Datenbank "TestDB" hat eine Tabelle mit dem Namen "Events".

```kusto
.show database TestDB schema 
```

**Beispielausgabe**

|DatabaseName|TableName|ColumnName|ColumnType|Isdefaulbar|Isdefaultcolumn|Prettyname|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|Ereignisse|||True|False||       
|TestDB|Ereignisse| Name|System.String|True|False||     
|TestDB|Ereignisse| StartTime|  System.DateTime|True|False||    
|TestDB|Ereignisse| EndTime|    System.DateTime|True|False||        
|TestDB|Ereignisse| City|   System.String|True| False||     
|TestDB|Ereignisse| SessionID|  System. Int32|True|  True|| 

**Beispiel** 
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```
**Beispielausgabe**

|DatabaseName|TableName|ColumnName|ColumnType|Isdefaulbar|Isdefaultcolumn|Prettyname|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v. 1.1       
|TestDB|Ereignisse|||True|False||       
|TestDB|Ereignisse| Name|System.String|True|False||     
|TestDB|Ereignisse| StartTime|  System.DateTime|True|False||    
|TestDB|Ereignisse| EndTime|    System.DateTime|True|False||        
|TestDB|Ereignisse| City|   System.String|True| False||     
|TestDB|Ereignisse| SessionID|  System. Int32|True|  True||  

Da eine niedrigere Version als die aktuelle Datenbankversion bereitgestellt wurde, wurde das Schema "TestDB" zurückgegeben. Das Bereitstellen einer gleichen oder einer höheren Version hätte ein leeres Ergebnis generiert.

**Beispiel** 
 
```kusto
.show database TestDB schema as json
```

**Beispielausgabe**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

