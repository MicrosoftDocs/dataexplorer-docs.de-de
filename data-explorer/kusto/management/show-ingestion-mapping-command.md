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
ms.openlocfilehash: 3c19426410046d7ff2357b4967333db8b039d9e6
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328992"
---
# <a name="show-ingestion-mappings"></a>. Zuordnungs Zuordnungen anzeigen

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

| Name     | Art | Zuordnung     |
|----------|------|-------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
