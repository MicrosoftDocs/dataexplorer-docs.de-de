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
ms.openlocfilehash: f5ffc7ae648a9254564af0705cda84f3c79da99b
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966940"
---
# <a name="ingestiontime-policy-command"></a>IngestionTime-Richtlinienbefehl

Die ingestiontime-Richtlinie ist eine optionale Richtlinie, die für Tabellen festgelegt ist (standardmäßig aktiviert).
Sie stellt die ungefähre Zeit für die Erfassung der Datensätze in einer Tabelle bereit.

Der Erfassungs Zeitwert kann zur Abfragezeit mithilfe der-Funktion aufgerufen werden `ingestion_time()` .

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