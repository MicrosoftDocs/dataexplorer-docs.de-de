---
title: RowOrder-Richtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die RowOrder-Richtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: cda4c9a6017071878832fab376a0376d250f3ed6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520246"
---
# <a name="roworder-policy"></a>RowOrder-Richtlinie

In diesem Artikel werden Steuerbefehle beschrieben, die zum Erstellen und Ändern der [Zeilenreihenfolgerichtlinie](../management/roworderpolicy.md)verwendet werden.

## <a name="show-roworder-policy"></a>RowOrder-Richtlinie anzeigen

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>RowOrder-Richtlinie löschen

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Alter RowOrder-Richtlinie

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**Beispiele**

Im folgenden Beispiel wird die Zeilenreihenfolgerichtlinie als Primärschlüssel in der `TenantId` Spalte `Timestamp` (aufsteigend) und in der Spalte (aufsteigend) als sekundärer Schlüssel festgelegt. anschließend wird die Richtlinie abgefragt:

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderPolicy| 
|---|---|
|events|(TenantId asc, Timestamp desc)| 