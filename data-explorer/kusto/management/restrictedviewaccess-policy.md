---
title: Kusto restrictedviewaccess Policy Management-Azure Daten-Explorer
description: Erfahren Sie mehr über die Einschränkungen der restrictedviewaccess-Richtlinie in Azure Daten-Explorer. Weitere Informationen finden Sie unter anzeigen, aktivieren, deaktivieren, ändern und Löschen dieser Richtlinie.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9ec328e3a15af3cedb833354f7e8ecea0fdc22c8
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002913"
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
