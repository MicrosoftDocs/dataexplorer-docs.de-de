---
title: .drop-Datenbank hübschname - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt .drop Datenbank prettyname in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8a74b871ddcf420f39bb45ebdf3bce36f026105
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521181"
---
# <a name="drop-database-prettyname"></a>.drop Datenbank hübschname

Löscht den hübschen (freundlichen) Namen einer Datenbank.
Erfordert [DatabaseAdmin-Berechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.drop``database` *Datenbankname*`prettyname`

**Return-Ausgabe**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName |String |Der Datenbankname.
|PrettyName |String |Der hübsche Name der Datenbank (null nach Demintierungsvorgang)