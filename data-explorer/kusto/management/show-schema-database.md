---
title: .anzeigen Datenbankschema - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird das Datenbankschema in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 212aaf428e03190226a17509c97e9acd1394d2b1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519804"
---
# <a name="show-databases-schema"></a>.show Datenbankschema

Gibt eine flache Liste der Struktur der ausgewählten Datenbanken mit allen tabellen- und spaltentabellen in einer einzelnen Tabelle oder einem JSON-Objekt zurück.
Bei Verwendung mit einer Version wird die Datenbank nur zurückgegeben, wenn es sich um eine spätere Version als die bereitgestellte Version handelt.

> [!NOTE]
> Die Version sollte nur im "vMM.mm"-Format bereitgestellt werden. MM stellt die Hauptversion und mm die Nebenversion dar.

`.show``database` *Datenbankname* `schema` `details`[`if_later_than` ] [ *"Version"*] 

`.show``database` *Datenbankname* `schema` `if_later_than` [ *"Version"*] `as``json`
 
`.show``databases` `,` DatabaseName1 ... *DatabaseName1* `(` `)` `schema` [`details` | `as` `json`]
 
`.show``databases` `,` *"Version"* *DatabaseName1* DatabaseName1 if_later_than "Version" ... `(` `)` `schema` [`details` | `as` `json`]

**Beispiel** 
 
Die Datenbank 'TestDB' hat 1 Tabelle mit dem Namen 'Events'.

```
.show database TestDB schema 
```

**Beispielausgabe**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|Ereignisse|||True|False||       
|TestDB|Ereignisse| name|System.String|True|False||     
|TestDB|Ereignisse| StartTime|  System.DateTime|True|False||    
|TestDB|Ereignisse| EndTime|    System.DateTime|True|False||        
|TestDB|Ereignisse| City|   System.String|True| False||     
|TestDB|Ereignisse| SessionID|  System. Int32|True|  True|| 

**Beispiel** 
 
```
.show database TestDB schema if_later_than "v1.0" 
```
**Beispielausgabe**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|Version
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|Ereignisse|||True|False||       
|TestDB|Ereignisse| name|System.String|True|False||     
|TestDB|Ereignisse| StartTime|  System.DateTime|True|False||    
|TestDB|Ereignisse| EndTime|    System.DateTime|True|False||        
|TestDB|Ereignisse| City|   System.String|True| False||     
|TestDB|Ereignisse| SessionID|  System. Int32|True|  True||  

Da eine Version bereitgestellt wurde, die niedriger als die aktuelle Datenbankversion ist, wurde das Schema "TestDB" zurückgegeben. Die Bereitstellung einer gleichen oder höheren Version hätte ein leeres Ergebnis generiert.

**Beispiel** 
 
```
.show database TestDB schema as json
```

**Beispielausgabe**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

