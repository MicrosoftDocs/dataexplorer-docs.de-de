---
title: . Alter Database Prettyname-Azure Daten-Explorer
description: In diesem Artikel wird der Befehl für den `.alter` Datenbankrecht Namen beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fc445a7d85f52d672b92163cc358d8f3a741c68
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294507"
---
# <a name="alter-database-prettyname"></a>. Alter Database Prettyname

Ändert den hübschen (freundlichen) Namen einer Datenbank.

Erfordert die [databaseadmin-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.alter``database` *DatabaseName* `prettyname` `'` *Databaseprettyname*`'`

**Ausgabe zurückgeben**
 
|Output-Parameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName |Zeichenfolge |Der Name der Datenbank.
|Prettyname |Zeichenfolge |Der Recht Name der Datenbank
