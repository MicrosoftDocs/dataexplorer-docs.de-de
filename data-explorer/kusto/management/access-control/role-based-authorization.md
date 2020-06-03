---
title: Rollenbasierte Autorisierung in Kusto-Azure Daten-Explorer
description: Dieser Artikel beschreibt die rollenbasierte Autorisierung in Kusto in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 8e961d389b365d77c7dddd557a28a158add2e3f3
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329077"
---
# <a name="role-based-authorization-in-kusto"></a>Rollenbasierte Autorisierung in Kusto

Die **Autorisierung** ist der Prozess, bei dem eine Sicherheits Prinzipal Berechtigung zum Ausführen einer Aktion zugelassen oder verweigert wird.
Kusto verwendet ein **rollenbasiertes Zugriffs Steuerungs** Modell, unter dem authentifizierte Prinzipale **Rollen**zugeordnet werden, und erhält Zugriff entsprechend der zugewiesenen Rollen.

Der **Kusto-Engine** -Dienst verfügt über die folgenden Rollen:

|Role                       |Berechtigungen                                                                                                                                                  |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Administrator für alle Datenbanken        |Kann im Bereich einer beliebigen Datenbank eine beliebige Aktion ausführen. Kann bestimmte Richtlinien auf Cluster Ebene anzeigen und ändern.                                                               |
|Database admin (Datenbankadministrator)             |Kann beliebige Aktionen im Bereich einer bestimmten Datenbank ausführen                                                                                                         |
|Datenbankbenutzer              |Kann alle Daten und Metadaten der Datenbank lesen. Darüber hinaus können von Tabellen erstellt und der Tabellen Administrator für diese Tabellen erstellt und Funktionen in der Datenbank erstellt werden.|
|Viewer für alle Datenbanken       |Kann alle Daten und Metadaten jeder beliebigen Datenbank lesen.                                                                                                               |
|Database viewer (Anzeigender Datenbankbenutzer)            |Kann alle Daten und Metadaten einer bestimmten Datenbank lesen.                                                                                                       |
|Database ingestor (Datenbankerfasser)          |Kann Daten für alle vorhandenen Tabellen in der Datenbank erfassen, aber keine Daten Abfragen.                                                                             |
|Database unrestrictedviewer (Nicht eingeschränkter anzeigender Datenbankbenutzer)|Kann alle Tabellen in der Datenbank Abfragen, für die die [restrictedviewaccess-Richtlinie](../restrictedviewaccess-policy.md) aktiviert ist.                                |
|Database monitor (Datenbankmonitor)           |Kann `.show` Befehle im Kontext der Datenbank und der zugehörigen untergeordneten Entitäten ausführen.                                                                           |
|Funktions Administrator             |Kann Funktion ändern, Funktion löschen oder Administrator Berechtigungen einem anderen Prinzipal erteilen                                                                         |
|Table admin (Tabellenadministrator)                |Kann beliebige Aktionen im Bereich einer bestimmten Tabelle ausführen.                                                                                                           |
|Table ingestor (Tabellenerfasser)             |Kann Daten im Gültigkeitsbereich einer bestimmten Tabelle erfassen, aber keine Daten Abfragen.                                                                                 |
