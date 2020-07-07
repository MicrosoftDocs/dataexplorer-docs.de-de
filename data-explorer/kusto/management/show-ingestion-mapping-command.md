---
title: . einerfassungs Zuordnungen anzeigen-Azure Daten-Explorer
description: In diesem Artikel wird beschrieben, wie Sie Erfassungs Zuordnungen in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 4a225c7d9cb1c5f99434c4595cbd798d020cfd9f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967433"
---
# <a name="show-ingestion-mapping"></a>.show ingestion mapping

Zeigt die Erfassungs Zuordnungen an (alle oder die durch den Namen angegebenen).

* `.show``table` *TableName* `ingestion` *mappingkind*  `mappings`

* `.show``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName*   

Alle Erfassungs Zuordnungen von allen Zuordnungs Typen anzeigen:

* `.show` `table` *TableName* `ingestion`  `mapping`
 
**Beispiel** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**Beispielausgabe**

| Name     | Variante | Zuordnung     |
|----------|------|-------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
