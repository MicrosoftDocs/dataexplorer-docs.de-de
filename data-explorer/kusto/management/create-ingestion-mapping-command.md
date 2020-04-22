---
title: .create-Erfassungszuordnung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die .create-Erfassungszuordnung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 10e656b074516ad8b0018e627d9904251aebbf10
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744497"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Erstellt eine Erfassungszuordnung, die einer bestimmten Tabelle und einem bestimmten Format zugeordnet ist.

**Syntax**

`.create``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Nach der Erstellung kann auf die Zuordnung anhand ihres Namens in Aufnahmebefehlen verwiesen werden, anstatt die vollständige Zuordnung als Teil des Befehls anzugeben.
> * Gültige Werte für _MappingKind_ `CSV`sind: `JSON`, , `avro`, `parquet`, und`orc`
> * Wenn für die Tabelle bereits eine Zuordnung mit demselben Namen vorhanden ist:
>    * `.create`wird scheitern
>    * `.create-or-alter`ändert die vorhandene Zuordnung
 
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
| Mapping1 | CSV  | ['Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null','Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null'] |
