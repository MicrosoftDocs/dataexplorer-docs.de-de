---
title: IngestionTime-Richtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die IngestionTime-Richtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c42da570b961595be1fbcae352fe121d8b6f59ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520892"
---
# <a name="ingestiontime-policy"></a>IngestionTime-Richtlinie

Die IngestionTime-Richtlinie ist eine optionale Richtlinie, die für Tabellen festgelegt ist (standardmäßig aktiviert).
Es gibt die ungefähre Zeit der Aufnahme der Datensätze in eine Tabelle an.

Auf den Aufnahmezeitwert kann zur `ingestion_time()` Abfragezeit mithilfe der Funktion zugegriffen werden.

```kusto
T | extend ingestionTime = ingestion_time()
```

So aktivieren/deaktivieren Sie die Richtlinie:
```kusto
.alter table table_name policy ingestiontime true
```

So aktivieren/deaktivieren Sie die Richtlinie mehrerer Tabellen:
```kusto
.alter tables (table_name [, ...]) policy ingestiontime true
```

So zeigen Sie die Richtlinie an:
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

So löschen Sie die Richtlinie (gleich der Deaktivierung):
```kusto
.delete table table_name policy ingestiontime  
```