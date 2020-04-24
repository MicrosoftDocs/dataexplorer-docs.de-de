---
title: .alter-Datenbank hübschname - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird .alter database prettyname in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b362b547dc980676108ec169a0abb97f189375b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522592"
---
# <a name="alter-database-prettyname"></a>.alter Datenbank hübschname

Ändert den hübschen (freundlichen) Namen einer Datenbank.

Erfordert [DatabaseAdmin-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.alter``database` *DatabaseName* `prettyname` DatabaseName `'` *DatabasePrettyName*`'`

**Return-Ausgabe**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName |String |Der Name der Datenbank.
|PrettyName |String |Der hübsche Name der Datenbank.

