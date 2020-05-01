---
title: . einerfassungs Zuordnungen anzeigen-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Erfassungs Zuordnungen in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 711861a07896b7bdc4cf57bebbf1cdd0e01d064a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617169"
---
# <a name="show-ingestion-mappings"></a>. Zuordnungs Zuordnungen anzeigen

Zeigt die Erfassungs Zuordnungen an (alle oder die durch den Namen angegebenen).

* `.show``table` *TableName* `ingestion` *mappingkind*  `mappings`

* `.show``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName*   

Alle Erfassungs Zuordnungen von allen mappingarten anzeigen:

* `.show``table` *TableName*`ingestion`  `mapping`
 
**Beispiel** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**Beispielausgabe**

| Name     | Variante | Zuordnung     |
|----------|------|-------------|
| mapping1 | CSV  | [{"Name": "RowNumber", "DataType": "int", "csvdatatype": NULL, "Ordinal": 0, "constvalue": NULL}, {"Name": "ROWGUID", "DataType": "String", "csvdatatype": NULL, "Ordinal": 1, "constvalue": NULL}] |
