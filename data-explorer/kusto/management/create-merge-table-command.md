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
ms.openlocfilehash: 554f6ed623b5a3be12a360fab0b1d5aa6eb4c084
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320960"
---
# <a name="create-merge-table"></a>.create-merge table

Erstellt eine neue Tabelle oder erweitert eine vorhandene Tabelle. 

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden. 

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

## <a name="syntax"></a>Syntax

`.create-merge``table` *TableName* ([columnName: ColumnType],...)  [ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*] `)` ]

Wenn die Tabelle nicht vorhanden ist, funktioniert genau wie der `.create table` Befehl.

Wenn Tabelle T vorhanden ist und Sie einen `.create-merge table T (<columns specification>)` Befehl senden, wird Folgendes angezeigt:

* Alle Spalten in <columns specification> , die zuvor nicht in t vorhanden waren, werden am Ende des t-Schemas hinzugefügt.
* Alle Spalten in t, die nicht in vorhanden sind, <columns specification> werden nicht aus t entfernt.
* Jede Spalte in <columns specification> , die in T vorhanden ist, aber mit einem anderen Datentyp, bewirkt, dass der Befehl fehlschlägt.

## <a name="see-also"></a>Weitere Informationen

* [`.create-merge tables`](create-merge-tables-command.md)
* [`.create table`](create-table-command.md)
