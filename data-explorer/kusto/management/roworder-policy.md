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
ms.openlocfilehash: bf8844a72d2b4fc3d9dec4f24fdfa6ac36ee84d7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967501"
---
# <a name="roworder-policy-command"></a>rowOrder Policy-Befehl

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
