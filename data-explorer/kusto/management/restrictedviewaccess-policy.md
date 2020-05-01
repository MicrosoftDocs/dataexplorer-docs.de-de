---
title: Kusto restrictedviewaccess Policy Management-Azure Daten-Explorer
description: In diesem Artikel wird die restrictedviewaccess-Richtlinie in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9da59a53819396cf2cbd522f4a1e1296f585bf2f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617546"
---
# <a name="restrictedviewaccess-policy"></a>Restrictedviewaccess-Richtlinie

Die *restrictedviewaccess* -Richtlinie ist [hier](../management/restrictedviewaccesspolicy.md)dokumentiert.

Kontroll Befehle zum Aktivieren oder Deaktivieren der Richtlinie für Tabellen in der Datenbank lauten wie folgt:

So aktivieren/deaktivieren Sie die Richtlinie:
```kusto
.alter table TableName policy restricted_view_access true|false
```

So aktivieren/deaktivieren Sie die Richtlinie mehrerer Tabellen:
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

Anzeigen der Richtlinie:
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

So löschen Sie die Richtlinie (entspricht der Deaktivierung):
```kusto
.delete table TableName policy restricted_view_access  
```