---
title: '. Erstellen einer Erfassungs Zuordnung: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt das Erstellen einer Erfassungs Zuordnung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3855d56ad31bbf98a6a075feb44a598b3bdbf52a
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329060"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Erstellt eine Erfassungs Zuordnung, die einer bestimmten Tabelle und einem bestimmten Format zugeordnet ist.

**Syntax**

`.create``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName* *mappingformattedasjson*

> [!NOTE]
> * Nachdem die Zuordnung erstellt wurde, kann Ihr Name in Erfassungs Befehlen referenziert werden, anstatt die gesamte Zuordnung als Teil des Befehls anzugeben.
> * Gültige Werte für _mappingkind_ sind: `CSV` , `JSON` , `avro` , `parquet` und.`orc`
> * Wenn für die Tabelle bereits eine Zuordnung mit dem gleichen Namen vorhanden ist:
>    * `.create`schlägt fehl
>    * `.create-or-alter`ändert die vorhandene Zuordnung.
 
**Beispiel** 
 
```kusto
.create table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'

.create-or-alter table MyTable ingestion json mapping "Mapping1"
'['
'    { "column" : "rownumber", "datatype" : "int", "Properties":{"Path":"$.rownumber"}},'
'    { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**Beispielausgabe**

| Name     | Art | Zuordnung                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Erfassung von Zuordnungen finden Sie unter [Daten](mappings.md)Zuordnungen.
