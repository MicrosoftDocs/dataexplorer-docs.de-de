---
title: 'Kusto restrictedviewaccess-Richtlinie steuert Abfragen: Azure Daten-Explorer'
description: In diesem Artikel wird die restrictedviewaccess-Richtlinie in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9ba9f8b0f0a940357eab2277eb24e18b92718bc4
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85901814"
---
# <a name="restrictedviewaccess-policy"></a>Restrictedviewaccess-Richtlinie

*Restrictedviewaccess* ist eine optionale Richtlinie, die für Tabellen einer Datenbank aktiviert werden kann.

Wenn diese Richtlinie für eine Tabelle aktiviert ist, können die Daten in der Tabelle nur von Prinzipale abgefragt werden, die über eine [unrestrictedviewer](../management/access-control/role-based-authorization.md) -Rolle in der Datenbank verfügen.
Jeder Prinzipal, der nicht bei einer [unrestrictedviewer](../management/access-control/role-based-authorization.md) -Rolle auf Datenbankebene registriert ist, kann keine Daten in der Tabelle Abfragen. Sogar eine nicht registrierte Tabelle/Datenbank/Cluster-admin.

Die [unrestrictedviewer](../management/access-control/role-based-authorization.md) -Rolle gewährt *allen* Tabellen in der Datenbank, für die die Richtlinie aktiviert ist, die VIEW-Berechtigung.
Der aktuelle Prinzipal, ein Datenbankadministrator/Benutzer/Viewer, ist bereits zum Abfragen der Datenbank autorisiert. Das Hinzufügen oder Entfernen von Prinzipale kann von einem [databaseadmin](../management/access-control/role-based-authorization.md)durchgeführt werden.

> [!NOTE]
> Die restrictedviewaccess-Richtlinie kann nicht für eine Tabelle konfiguriert werden, für die eine [Sicherheit auf Zeilenebene Richtlinie](./rowlevelsecuritypolicy.md) aktiviert ist.

Weitere Informationen finden Sie unter den Steuerungs Befehlen zum Verwalten der [restrictedviewaccess-Richtlinie](../management/restrictedviewaccess-policy.md).
