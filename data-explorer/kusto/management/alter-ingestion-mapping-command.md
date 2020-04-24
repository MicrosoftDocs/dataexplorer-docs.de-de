---
title: .ändern-Erfassungszuordnung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die .alter-Erfassungszuordnung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5343d55fadafce552c5d837e5eb50763ccf45a4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522405"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Ändert eine vorhandene Aufnahmezuordnung, die einer bestimmten Tabelle und einem bestimmten Format zugeordnet ist (vollständige Zuordnung ersetzen).

**Syntax**

`.alter``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Auf diese Zuordnung kann anhand von Aufnahmebefehlen anhand ihres Namens verwiesen werden, anstatt die vollständige Zuordnung als Teil des Befehls anzugeben.
> * Gültige Werte für _MappingKind_ sind: `CSV`, `JSON`, `avro`, `parquet`, und `orc`.

**Beispiel** 
 
```
.alter table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"} }'
']'

.alter table MyTable ingestion json mapping "Mapping1"
'['
'   { "column" : "rownumber", "Properties":{"Path":"$.rownumber"}},'
'   { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```
**Beispielausgabe**

| name     | Variante | Zuordnung                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Mapping1 | CSV  | ['Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null','Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null'] |