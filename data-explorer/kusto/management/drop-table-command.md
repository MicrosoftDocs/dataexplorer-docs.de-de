---
title: .drop-Tabelle und .drop-Tabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden .drop-Tabellen und .drop-Tabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 5f3a488aba5a6785ceb6ad4a093c520ec0509e5e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744753"
---
# <a name="drop-table-and-drop-tables"></a>.drop-Tabelle und .drop-Tabellen

Entfernt eine Tabelle (oder mehrere Tabellen) aus der Datenbank.

Erfordert [die Berechtigung des Tabellenadministrators](../management/access-control/role-based-authorization.md).

> [!NOTE]
> Der `.drop` `table` Befehl nur soft löscht die Daten (d. h. Daten können nicht abgefragt werden, können aber dennoch aus persistentem Speicher wiederhergestellt werden). Die zugrunde liegenden Speicherartefakte werden entsprechend `recoverability` der Eigenschaft in der [Aufbewahrungsrichtlinie,](../management/retentionpolicy.md) die zum Zeitpunkt der Aufnahme der Daten in die Tabelle galt, hart gelöscht.

**Syntax**

`.drop``table` *Tabellenname* `ifexists`[ ]

`.drop``tables` (*TableName1*, *TableName2*,..) [ifexists]

> [!NOTE]
> Wenn `ifexists` angegeben, schlägt der Befehl nicht fehl, wenn eine Tabelle nicht vorhanden ist.

**Beispiel**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**Rückgabe**

Dieser Befehl gibt eine Liste der verbleibenden Tabellen in der Datenbank zurück. 

| Ausgabeparameter | type   | BESCHREIBUNG                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Der Name der Tabelle.                  |
| DatabaseName     | String | Die Datenbank, zu der die Tabelle gehört. |