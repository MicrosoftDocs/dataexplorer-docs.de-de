---
title: . Drop Erfassungs Zuordnung-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Erfassungs Zuordnung in Azure Daten-Explorer ablegen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 7454bd86a6ca2a835dc0515a9c8901a444259f12
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294524"
---
# <a name="drop-ingestion-mapping"></a>.drop ingestion mapping

Löscht die Erfassungs Zuordnung aus der Datenbank.
 
`.drop``table` *TableName* `ingestion` *mappingkind* `mapping` *MappingName*   

**Beispiel** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
