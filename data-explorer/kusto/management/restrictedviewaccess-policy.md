---
title: RestrictedViewAccess-Richtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die RestrictedViewAccess-Richtlinie in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9fcf37d30bfe3ab0f9c4b5d4a720e6a5ba4ffe34
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520450"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess-Richtlinie

Die *RestrictedViewAccess-Richtlinie* wird [hier](../management/restrictedviewaccesspolicy.md)dokumentiert.

Die folgenden Steuerungsbefehle zum Aktivieren oder Deaktivieren der Richtlinie für Tabellen in der Datenbank sind die folgenden:

So aktivieren/deaktivieren Sie die Richtlinie:
```kusto
.alter table TableName policy restricted_view_access true|false
```

So aktivieren/deaktivieren Sie die Richtlinie mehrerer Tabellen:
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

So zeigen Sie die Richtlinie an:
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

So löschen Sie die Richtlinie (entspricht der Deaktivierung):
```kusto
.delete table TableName policy restricted_view_access  
```