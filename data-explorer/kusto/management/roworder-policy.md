---
title: 'RowOrder-Richtlinie: Azure-Daten-Explorer'
description: Dieser Artikel beschreibt die rowOrder-Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 63aad71854c73a3d1f1837c3665a152db8b48d13
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258027"
---
# <a name="roworder-policy"></a>RowOrder-Richtlinie

In diesem Artikel werden Steuerungsbefehle zum Erstellen und Ändern der [Richtlinie für die Zeilen Reihenfolge](../management/roworderpolicy.md)beschrieben

## <a name="show-roworder-policy"></a>RowOrder-Richtlinie anzeigen

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>RowOrder-Richtlinie löschen

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Alter rowOrder-Richtlinie

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**Beispiele** 

Im folgenden Beispiel wird die Richtlinie für die Zeilen Reihenfolge für die `TenantId` Spalte (aufsteigend) als Primärschlüssel und für die `Timestamp` Spalte (aufsteigend) als sekundär Schlüssel festgelegt. Die Richtlinie wird dann abgefragt.

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|Roworderpolicy| 
|---|---|
|events|(Tenantid ASC, timestamp Entsc)|
