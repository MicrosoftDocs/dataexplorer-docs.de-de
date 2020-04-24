---
title: RestrictedViewAccess-Richtlinie - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die RestrictedViewAccess-Richtlinie in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 6f994f5b80632650ab6dbe5dcf28cd82407d688f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520416"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess-Richtlinie

*RestrictedViewAccess* ist eine optionale Richtlinie, die für Tabellen in einer Datenbank aktiviert werden kann.

Wenn diese Richtlinie für eine Tabelle aktiviert ist, können Daten in der Tabelle *nur* an Prinzipale abgefragt werden, die der [Rolle UnrestrictedViewer](../management/access-control/role-based-authorization.md) in der Datenbank hinzugefügt wurden.

Wenn eine Richtlinie für eine Tabelle aktiviert ist, kann kein Prinzipal (auch ein Tabellen-/Datenbank-/Clusteradministrator), der nicht in der Rolle ["UnrestrictedViewer"-Datenbankebene](../management/access-control/role-based-authorization.md) enthalten ist, Keine Daten in der Tabelle abfragen.

Die Rolle [UnrestrictedViewer](../management/access-control/role-based-authorization.md) erteilt *allen* Tabellen in der Datenbank, für die die Richtlinie aktiviert ist, Die Ansichtsberechtigung, vorausgesetzt, der aktuelle Prinzipal ist bereits berechtigt, die Datenbank abzufragen (ein Datenbankadministrator/Benutzer/Viewer). Das Hinzufügen oder Entfernen von Prinzipalen zu oder aus der Rolle kann von einem [DatabaseAdmin](../management/access-control/role-based-authorization.md)ausgeführt werden.

> [!NOTE]
> Die RestrictedViewAccess-Richtlinie kann nicht für eine Tabelle konfiguriert werden, für die eine Richtlinie auf [Zeilenebene](./rowlevelsecuritypolicy.md) aktiviert ist.

Weitere Informationen zu den Steuerbefehlen zum Verwalten der RestrictedViewAccess-Richtlinie [finden Sie hier](../management/restrictedviewaccess-policy.md).