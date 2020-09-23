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
ms.openlocfilehash: 19dc7db9e344a516b5c92917dccbf8362b1ca858
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102885"
---
# <a name="create-merge-table"></a>.create-merge table

Erstellt eine neue Tabelle oder erweitert eine vorhandene Tabelle. 

Der Befehl muss im Kontext einer bestimmten Datenbank ausgeführt werden. 

Erfordert die [Berechtigung für Datenbankbenutzer](../management/access-control/role-based-authorization.md).

## <a name="syntax"></a>Syntax

`.create-merge``table` *TableName* ([columnName: ColumnType],...)  [ `with` `(` [ `docstring` `=` *Dokumentation*] [ `,` `folder` `=` *FolderName*] `)` ]

Wenn die Tabelle nicht vorhanden ist, funktioniert genau wie der Befehl ". Create Table".

Wenn Tabelle t vorhanden ist, und Sie den Befehl ". Create-Merge Table T ( <columns specification> )" senden, wird Folgendes angezeigt:

* Alle Spalten in <columns specification> , die zuvor nicht in t vorhanden waren, werden am Ende des t-Schemas hinzugefügt.
* Alle Spalten in t, die nicht in vorhanden sind, <columns specification> werden nicht aus t entfernt.
* Jede Spalte in <columns specification> , die in T vorhanden ist, aber mit einem anderen Datentyp, bewirkt, dass der Befehl fehlschlägt.

## <a name="see-also"></a>Weitere Informationen

* [.create-merge tables](create-merge-tables-command.md)
* [. Create-Tabelle](create-table-command.md)
