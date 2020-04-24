---
title: Rollenbasierte Autorisierung in Kusto - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die rollenbasierte Autorisierung in Kusto in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: fe7013e3ee4b842dc2dcb2e251c342897fa4e489
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522626"
---
# <a name="role-based-authorization-in-kusto"></a>Rollenbasierte Autorisierung in Kusto



**Bei** der Autorisierung wird einem Sicherheitsprinzipal Berechtigungen zum Ausführen einer Aktion zugelassen oder nicht erteilt.
Kusto verwendet ein **rollenbasiertes Zugriffssteuerungsmodell,** bei dem authentifizierte Prinzipale **Rollen**zugeordnet werden und zugriffentsprechend den ihnen zugewiesenen Rollen erhalten.

Der **Kusto Engine-Dienst** hat die folgenden Rollen:

|Role                       |Berechtigungen                                                                                                                                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Alle Datenbanken admin        |Kann "alles" im Bereich jeder Datenbank tun; Kann bestimmte Richtlinien auf Clusterebene anzeigen und ändern.                                                           |
|Database admin (Datenbankadministrator)             |Kann "alles" im Bereich einer bestimmten Datenbank tun.                                                                                                     |
|Datenbankbenutzer              |Kann alle Daten und Metadaten der Datenbank lesen; können außerdem Tabellen (und damit Tabellenadministrator für diese Tabelle) und Funktionen in der Datenbank erstellen.|
|Alle Datenbanken Viewer       |Kann alle Daten und Metadaten jeder Datenbank lesen.                                                                                                              |
|Database viewer (Anzeigender Datenbankbenutzer)            |Kann alle Daten und Metadaten einer bestimmten Datenbank lesen.                                                                                                     |
|Database ingestor (Datenbankerfasser)          |Kann Daten in allen vorhandenen Tabellen in der Datenbank erfassen, die Daten aber nicht abfragen.                                                                              |
|Database unrestrictedviewer (Nicht eingeschränkter anzeigender Datenbankbenutzer)|Kann alle Tabellen in der Datenbank abfragen, für die die [RestrictedViewAccess-Richtlinie](../restrictedviewaccess-policy.md) aktiviert ist.                                |
|Database monitor (Datenbankmonitor)           |Kann `.show` Befehle im Kontext der Datenbank und ihrer untergeordneten Entitäten ausführen.                                                                          |
|Funktionsadministrator             |Kann die Funktion ändern, die Funktion löschen oder einem anderen Prinzipal Administratorberechtigungen erteilen.                                                                         |
|Table admin (Tabellenadministrator)                |Kann alle Aktionen im Bereich einer bestimmten Tabelle ausführen.                                                                                                          |
|Table ingestor (Tabellenerfasser)             |Kann Daten im Bereich einer bestimmten Tabelle erfassen, die Daten aber nicht abfragen.                                                                                  |
