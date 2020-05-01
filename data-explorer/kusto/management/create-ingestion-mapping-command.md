---
title: '. Erstellen einer Erfassungs Zuordnung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt das Erstellen einer Erfassungs Zuordnung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 84ab277f5b0d4d1b2e09d31fb7c1254786affe6d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617730"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Erstellt eine Erfassungs Zuordnung, die einer bestimmten Tabelle und einem bestimmten Format zugeordnet ist.

**Syntax**

`.create``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName* *mappingformattedasjson*

> [!NOTE]
> * Nachdem die Zuordnung erstellt wurde, kann Ihr Name in Erfassungs Befehlen referenziert werden, anstatt die gesamte Zuordnung als Teil des Befehls anzugeben.
> * Gültige Werte für _mappingkind_ sind: `CSV`, `JSON`, `avro`, `parquet`und.`orc`
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

| Name     | Variante | Zuordnung                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{"Name": "RowNumber", "DataType": "int", "csvdatatype": NULL, "Ordinal": 0, "constvalue": NULL}, {"Name": "ROWGUID", "DataType": "String", "csvdatatype": NULL, "Ordinal": 1, "constvalue": NULL}] |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Erfassung von Zuordnungen finden Sie unter [Daten](mappings.md)Zuordnungen.
