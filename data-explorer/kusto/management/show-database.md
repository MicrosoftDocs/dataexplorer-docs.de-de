---
title: '. Anzeigen der Datenbank: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie die Datenbank in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb3e1dd16d36af5d92019b710f3d5790a24b1296
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320484"
---
# <a name="show-database"></a>.show database

Gibt eine Tabelle zurück, in der die Eigenschaften der Kontext Datenbank angezeigt werden.

Informationen zum Zurückgeben einer Tabelle, in der jeder Datensatz einer Datenbank im Cluster entspricht, auf die der Benutzer Zugriff hat, finden Sie unter [`.show databases`](show-databases.md) .

**Syntax**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

Der standardmäßige-Befehl ohne angegebene Optionen entspricht der Option ' Identity '.

**Ausgabe für "Identity"-Option**
 
|Ausgabeparameter |type |Beschreibung 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird Groß-/Kleinschreibung beachtet. 
|Persistentstorage  |String |Der permanente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Versionsnummer der Datenbank. Diese Nummer wird für jeden Änderungs Vorgang in der Datenbank aktualisiert (z. b. das Hinzufügen von Daten und das Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank die Datenbank ist, auf die die aktuelle Verbindung verweist. 
|Databaseaccessmode  |String |Gibt an, wie der Cluster an die Datenbank angefügt wird. Wenn die Datenbank z. b. im schreibgeschützten Modus angefügt wird, kann der Cluster nicht alle Anforderungen zum Ändern der Datenbank ändern. 
|Prettyname |String |Der Name der Datenbank ist ziemlich.
|Currentuserisunrestrictedviewer |Boolean | Gibt an, ob der aktuelle Benutzer ein uneingeschränkter Viewer für die Datenbank ist.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.

**Ausgabe für die Option "Details"**
 
|Ausgabeparameter |type |Beschreibung 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird Groß-/Kleinschreibung beachtet. 
|Persistentstorage  |String |Der permanente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Die Versionsnummer der Datenbank. Diese Nummer wird für jeden Änderungs Vorgang in der Datenbank aktualisiert (z. b. das Hinzufügen von Daten und das Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank die Datenbank ist, auf die die aktuelle Verbindung verweist. 
|Databaseaccessmode  |String |Gibt an, wie der Cluster an die Datenbank angefügt wird. Wenn die Datenbank z. b. im schreibgeschützten Modus angefügt wird, kann der Cluster nicht alle Anforderungen zum Ändern der Datenbank ändern. 
|Prettyname |String |Der Name der Datenbank ist ziemlich.
|Authorizedprincipals |String | Die Auflistung der autorisierten Prinzipale der Datenbank (im JSON-Format serialisiert).
|RetentionPolicy |String | Die Beibehaltungs Richtlinie der Datenbank (im JSON-Format serialisiert).
|Mergepolicy |String | Die zusammenlaufsrichtlinie für die Daten Bank Blöcke (im JSON-Format serialisiert).
|Cachingpolicy |String | Die Cachingrichtlinie der Datenbank (im JSON-Format serialisiert).
|Shardingpolicy |String | Die Sharding-Richtlinie der Datenbank (im JSON-Format serialisiert).
|Streamingingestionpolicy |String | Die Richtlinie für die streamingansung der Datenbank (im JSON-Format serialisiert).
|Ingestionbatchingpolicy |String | Die Erfassungs Richtlinie für die Erfassung der Datenbank (im JSON-Format serialisiert).
|TotalSize |Real | Die Gesamtgröße der Daten Bank Blöcke.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.

**Ausgabe für die Option "Richtlinien"**
 
|Ausgabeparameter |type |Beschreibung 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird Groß-/Kleinschreibung beachtet. 
|Persistentstorage  |String |Der permanente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Die Versionsnummer der Datenbank. Diese Nummer wird für jeden Änderungs Vorgang in der Datenbank aktualisiert (z. b. das Hinzufügen von Daten und das Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank die Datenbank ist, auf die die aktuelle Verbindung verweist. 
|Databaseaccessmode  |String |Gibt an, wie der Cluster an die Datenbank angefügt wird. Wenn die Datenbank z. b. im schreibgeschützten Modus angefügt wird, kann der Cluster nicht alle Anforderungen zum Ändern der Datenbank ändern. 
|Prettyname |String |Der Name der Datenbank.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.
|Authorizedprincipals |String | Die Auflistung der autorisierten Prinzipale der Datenbank (im JSON-Format serialisiert).
|RetentionPolicy |String | Die Beibehaltungs Richtlinie der Datenbank (im JSON-Format serialisiert).
|Mergepolicy |String | Die zusammenlaufsrichtlinie für die Daten Bank Blöcke (im JSON-Format serialisiert).
|Cachingpolicy |String | Die Cachingrichtlinie der Datenbank (im JSON-Format serialisiert).
|Shardingpolicy |String | Die Sharding-Richtlinie der Datenbank (im JSON-Format serialisiert).
|Streamingingestionpolicy |String | Die Richtlinie für die streamingansung der Datenbank (im JSON-Format serialisiert).
|Ingestionbatchingpolicy |String | Die Erfassungs Richtlinie für die Erfassung der Datenbank (im JSON-Format serialisiert).

**Ausgabe für die Option "datastats"**

|Ausgabeparameter |type |Beschreibung 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird Groß-/Kleinschreibung beachtet.
|Persistentstorage  |String |Der permanente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Die Versionsnummer der Datenbank. Diese Nummer wird für jeden Änderungs Vorgang in der Datenbank aktualisiert (z. b. das Hinzufügen von Daten und das Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank die Datenbank ist, auf die die aktuelle Verbindung verweist. 
|Databaseaccessmode  |String |Gibt an, wie der Cluster an die Datenbank angefügt wird. Wenn die Datenbank z. b. im schreibgeschützten Modus angefügt wird, kann der Cluster nicht alle Anforderungen zum Ändern der Datenbank ändern. 
|Prettyname |String |Der Name der Datenbank.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.
|OriginalSize |Real | Die Gesamtgröße der Daten Bank Blöcke gesamt.
|Extentsize |Real | Die Gesamtgröße der Daten Bank Blöcke (Daten + Indizes).
|CompressedSize |Real | Die Größe der Daten Bank Blöcke Gesamtgröße der komprimierten Daten.
|IndexSize |Real | Die Gesamtgröße der Daten Bank Blöcke Gesamtindex Größe.
|RowCount |Long | Die Gesamtanzahl der Zeilen für die Daten Bank Blöcke.
|Hotoriginalsize |Real | Die Gesamtgröße der Datenbank ist die Gesamtgröße der ursprünglichen Größe.
|"Hutextentsize" |Real | Die Gesamtgröße der Daten Bank Blöcke (Daten + Indizes).
|Hotcompressedsize |Real | Die Gesamtgröße der Daten Bank Blöcke Gesamtgröße der komprimierten Daten.
|Hotindexsize |Real | Die Gesamtgröße der Daten Bank Blöcke Gesamt, Indexgröße.
|"Hatrowcount" |Long | Die Gesamtanzahl der Zeilen in der Datenbank.