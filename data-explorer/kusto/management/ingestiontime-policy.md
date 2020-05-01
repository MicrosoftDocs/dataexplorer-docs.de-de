---
title: Kusto ingestiontime-Richtlinien Verwaltung-Azure Daten-Explorer
description: Dieser Artikel beschreibt die ingestiontime-Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 907c6ddf84d772f800fce45d3c1245bbd11b0c85
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616455"
---
# <a name="ingestiontime-policy"></a>IngestionTime-Richtlinie

Die ingestiontime-Richtlinie ist eine optionale Richtlinie, die für Tabellen festgelegt ist (standardmäßig aktiviert).
Sie stellt die ungefähre Zeit für die Erfassung der Datensätze in einer Tabelle bereit.

Der Erfassungs Zeitwert kann zur Abfragezeit mithilfe `ingestion_time()` der-Funktion aufgerufen werden.

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

Anzeigen der Richtlinie:
```kusto
.show table table_name policy ingestiontime  

.show table * policy ingestiontime  
```

So löschen Sie die Richtlinie (entspricht der Deaktivierung):
```kusto
.delete table table_name policy ingestiontime  
```