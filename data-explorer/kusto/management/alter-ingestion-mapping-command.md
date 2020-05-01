---
title: . Alter Erfassung Mapping-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Alter-Erfassung in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f43039ff3935edbb6e92627d2f96b1c411e1ffa
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617815"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Ändert eine vorhandene Erfassungs Zuordnung, die einer bestimmten Tabelle zugeordnet ist, und ein bestimmtes Format (vollständiges Mapping ersetzen).

**Syntax**

`.alter``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName* *mappingformattedasjson*

> [!NOTE]
> * Auf diese Zuordnung kann über den Namen durch Erfassungs Befehle verwiesen werden, anstatt die gesamte Zuordnung als Teil des Befehls anzugeben.
> * Gültige Werte für _mappingkind_ sind: `CSV`, `JSON`, `avro`, `parquet`und `orc`.

**Beispiel** 
 
```kusto
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

| Name     | Variante | Zuordnung                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{"Name": "RowNumber", "DataType": "int", "csvdatatype": NULL, "Ordinal": 0, "constvalue": NULL}, {"Name": "ROWGUID", "DataType": "String", "csvdatatype": NULL, "Ordinal": 1, "constvalue": NULL}] |
