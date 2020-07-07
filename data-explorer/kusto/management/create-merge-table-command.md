---
title: '. Create-MERGE-Tabelle: Azure-Daten-Explorer'
description: In diesem Artikel wird die CREATE-MERGE-Tabelle in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: afe5011717fd77d654eaf6c2b70e9ffbdea87128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967624"
---
# <a name="create-merge-table"></a>. Create-MERGE-Tabelle

Erstellt eine neue Tabelle oder erweitert eine vorhandene Tabelle. 

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden. 

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

**Syntax**

`.create-merge``table` *TableName* ([columnName: ColumnType],...)  [ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*] `)` ]

Wenn die Tabelle nicht vorhanden ist, funktioniert genau wie der Befehl ". Create Table".

Wenn Tabelle t vorhanden ist, und Sie den Befehl ". Create-Merge Table T ( <columns specification> )" senden, wird Folgendes angezeigt:

* Alle Spalten in <columns specification> , die zuvor nicht in t vorhanden waren, werden am Ende des t-Schemas hinzugefügt.
* Alle Spalten in t, die nicht in vorhanden sind, <columns specification> werden nicht aus t entfernt.
* Jede Spalte in <columns specification> , die in T vorhanden ist, aber mit einem anderen Datentyp, bewirkt, dass der Befehl fehlschlägt.

**Siehe auch**

* [.create-merge tables](create-merge-tables-command.md)
* [.create table](create-table-command.md)
