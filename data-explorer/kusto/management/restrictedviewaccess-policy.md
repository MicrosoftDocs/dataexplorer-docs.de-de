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
ms.openlocfilehash: 33f21bdad11555ad2a55f285cbf40239236c561f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967603"
---
# <a name="restricted_view_access-policy-command"></a>Richtlinienbefehl für „restricted_view_access“

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