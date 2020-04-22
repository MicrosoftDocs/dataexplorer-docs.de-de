---
title: .drop-Erfassungszuordnung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die .drop-Erfassungszuordnung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c1434234033acc73de35289c6bc0a90af727babb
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744767"
---
# <a name="drop-ingestion-mapping"></a>.drop ingestion mapping

Löscht die Aufnahmezuordnung aus der Datenbank.
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**Beispiel** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion JSON mappings "Mapping1" 
```
