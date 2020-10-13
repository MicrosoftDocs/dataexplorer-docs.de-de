---
title: Kusto ingestiontime-Richtlinien Verwaltung-Azure Daten-Explorer
description: Machen Sie sich mit den Befehlen der ingestiontime-Richtlinie in Azure Daten-Explorer vertraut. Erfahren Sie, wie Sie auf Erfassungszeiten zugreifen, und wie Sie diese Richtlinie aktivieren und deaktivieren.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e1671373547a4ed069d67bc9153248f23bc3b5e
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003104"
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