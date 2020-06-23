---
title: . DROP TABLE-und Drop Tables-Azure-Daten-Explorer
description: In diesem Artikel wird beschrieben, wie Tabellen-und Drop-Tabellen in Azure Daten-Explorer abgelegt werden.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 3e1eb57741302d34664f6cd8f256612a6e70bdd1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264486"
---
# <a name="drop-table-and-drop-tables"></a>. DROP TABLE-und Drop-Tabellen

Entfernt eine Tabelle oder mehrere Tabellen aus der Datenbank.

Erfordert die [Table admin-Berechtigung](../management/access-control/role-based-authorization.md).

> [!NOTE]
> Mit dem `.drop` `table` Befehl werden die Daten nur vorläufig gelöscht. Das heißt, dass Daten nicht abgefragt werden können, aber dennoch aus dem permanenten Speicher wiederherstellbar sind. Die zugrunde liegenden Speicher Artefakte werden gemäß der `recoverability` Eigenschaft in der [Beibehaltungs Richtlinie](../management/retentionpolicy.md) fest gelöscht, die zum Zeitpunkt der Erfassung der Daten in der Tabelle wirksam war.

**Syntax**

`.drop``table` *Tabellenname* [ `ifexists` ]

`.drop``tables`(*TableName1*, *TableName2*,..) [ifist vorhanden]

> [!NOTE]
> Wenn `ifexists` angegeben wird, schlägt der Befehl nicht fehl, wenn eine nicht vorhandene Tabelle vorhanden ist.

**Beispiel**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**Rückgabe**

Mit diesem Befehl wird eine Liste der verbleibenden Tabellen in der Datenbank zurückgegeben.

| Output-Parameter | type   | BESCHREIBUNG                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Der Name der Tabelle.                  |
| DatabaseName     | String | Die Datenbank, zu der die Tabelle gehört. |
