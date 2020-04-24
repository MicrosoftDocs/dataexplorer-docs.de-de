---
title: Abfrageverwaltung - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Abfrageverwaltung in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: b888aa004b4c8b02cd4e1d130bb0b5319af018b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520518"
---
# <a name="queries-management"></a>Abfrageverwaltung

## <a name="show-queries"></a>.show-Abfragen

Der `.show` `queries` Befehl gibt eine Liste von Abfragen zurück, die einen endgültigen Status erreicht haben und dass der Benutzer, der den Befehl aufruft, Zugriff auf folgende Informationen hat:


* Ein [Datenbankadministrator oder Datenbankmonitor](../management/access-control/role-based-authorization.md) kann jeden Befehl sehen, der in seiner Datenbank aufgerufen wurde.
* Andere Benutzer können nur Abfragen sehen, die von ihnen aufgerufen wurden.

**Syntax**

`.show` `queries`

## <a name="show-running-queries"></a>.show ausgeführte Abfragen

`.show` `running` Der `queries` Befehl gibt eine Liste der aktuell ausgeführten Abfragen durch den Benutzer, einen anderen Benutzer oder alle Benutzer zurück.

**Syntax**

```kusto
.show running queries
```

* (1) gibt die aktuell ausgeführten Abfragen durch den aufrufenden Benutzer zurück (erfordert Lesezugriff).

## <a name="cancel-query"></a>.cancel-Abfrage

Der `.cancel` `query` Befehl startet einen Versuch, eine bestimmte Abfrage, die zuvor vom gleichen Benutzer gestartet wurde, abzubrechen.

* Clusteradministratoren können jede ausgeführte Abfrage abbrechen.
* Datenbankadministratoren können jede ausgeführte Abfrage abbrechen, die für eine Datenbank aufgerufen wurde, für die sie Administratorzugriff haben.
* Andere Benutzer können nur Abfragen abbrechen, die sie gestartet haben. 

**Syntax**

`.cancel``query` *ClientRequestId*

* *ClientRequestId* ist der Wert des ursprünglichen Queries-Felds ClientRequestId als `string` Literal.

**Beispiel**

```kusto
.cancel query "KE.RunQuery;8f70e9ab-958f-4955-99df-d2a288b32b2c"
```

