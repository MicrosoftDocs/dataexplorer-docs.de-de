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
ms.openlocfilehash: e44aa2b14aa8babdab95a6ad8c6f7ef5ed026ff9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617425"
---
# <a name="restrictedviewaccess-policy"></a>Restrictedviewaccess-Richtlinie

*Restrictedviewaccess* ist eine optionale Richtlinie, die für Tabellen in einer Datenbank aktiviert werden kann.

Wenn diese Richtlinie für eine Tabelle aktiviert ist, können die Daten in der Tabelle *nur* auf Prinzipale abgefragt werden, die der [unrestrictedviewer](../management/access-control/role-based-authorization.md) -Rolle in der Datenbank hinzugefügt wurden.

Wenn eine Richtlinie für eine Tabelle aktiviert ist, kann jeder Prinzipal (auch eine Tabelle/Datenbank/Cluster Administrator), der nicht in der [unrestrictedviewer](../management/access-control/role-based-authorization.md) -Rolle auf Datenbankebene enthalten ist, Daten in der Tabelle Abfragen.

Die [unrestrictedviewer](../management/access-control/role-based-authorization.md) -Rolle gewährt *allen* Tabellen in der Datenbank, für die die Richtlinie aktiviert ist, die VIEW-Berechtigung, vorausgesetzt, der aktuelle Prinzipal ist bereits zum Abfragen der Datenbank autorisiert (Datenbankadministrator/Benutzer/Viewer). Das Hinzufügen oder Entfernen von Prinzipale zu oder aus der Rolle kann von einem [databaseadmin](../management/access-control/role-based-authorization.md)durchgeführt werden.

> [!NOTE]
> Die restrictedviewaccess-Richtlinie kann nicht für eine Tabelle konfiguriert werden, für die eine [Sicherheit auf Zeilenebene Richtlinie](./rowlevelsecuritypolicy.md) aktiviert ist.

Weitere Informationen zu den Steuerungs Befehlen zum Verwalten der restrictedviewaccess-Richtlinie [finden Sie hier](../management/restrictedviewaccess-policy.md).