---
title: .show-Datenbank - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die .show-Datenbank in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 95d90377afd33971052dd00cd6ab3d662130824c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519872"
---
# <a name="show-database"></a>.show-Datenbank

Gibt eine Tabelle mit den Eigenschaften der Kontextdatenbank zurück.

Informationen zum Zurückgeben einer Tabelle, in der jeder Datensatz einer Datenbank im Cluster entspricht, auf die der Benutzer Zugriff hat, finden Sie unter [.show databases](show-databases.md).

**Syntax**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

Der Standardaufruf ohne angegebene Optionen ist gleich der Option "Identität".

**Ausgabe für 'Identität' Option**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird die Groß-/Kleinschreibung beachtet. 
|PersistentStorage  |String |Der persistente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Versionsnummer der Datenbank. Diese Nummer wird für jeden Änderungsvorgang in der Datenbank aktualisiert (z. B. Hinzufügen von Daten und Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank diejenige ist, auf die die aktuelle Verbindung verweist. 
|DatabaseAccessMode  |String |Wie der Cluster an die Datenbank angefügt ist. Wenn die Datenbank z. B. im ReadOnly-Modus angefügt ist, schlägt der Cluster alle Anforderungen fehl, um die Datenbank in irgendeiner Weise zu ändern. 
|PrettyName |String |Die Datenbank hübscher Name.
|CurrentUserIsUnrestrictedViewer |Boolean | Gibt an, ob der aktuelle Benutzer ein uneingeschränkter Viewer in der Datenbank ist.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.

**Ausgabe für 'Details' Option**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird die Groß-/Kleinschreibung beachtet. 
|PersistentStorage  |String |Der persistente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Die Datenbankversionsnummer. Diese Nummer wird für jeden Änderungsvorgang in der Datenbank aktualisiert (z. B. Hinzufügen von Daten und Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank diejenige ist, auf die die aktuelle Verbindung verweist. 
|DatabaseAccessMode  |String |Wie der Cluster an die Datenbank angefügt ist. Wenn die Datenbank z. B. im ReadOnly-Modus angefügt ist, schlägt der Cluster alle Anforderungen fehl, um die Datenbank in irgendeiner Weise zu ändern. 
|PrettyName |String |Die Datenbank hübscher Name.
|AuthorizedPrincipals |String | Die Sammlung autorisierter Prinzipale der Datenbank (serialisiert im JSON-Format).
|RetentionPolicy |String | Die Aufbewahrungsrichtlinie der Datenbank (serialisiert im JSON-Format).
|MergePolicy |String | Die Extents Merge-Richtlinie der Datenbank (serialisiert im JSON-Format).
|CachingPolicy |String | Die Caching-Richtlinie der Datenbank (serialisiert im JSON-Format).
|Sharding-Richtlinie |String | Die Sharding-Richtlinie der Datenbank (serialisiert im JSON-Format).
|StreamingIngestionPolicy |String | Die Streaming-Aufnahmerichtlinie der Datenbank (serialisiert im JSON-Format).
|IngestionBatchingPolicy |String | Die Ingestion Batching-Richtlinie der Datenbank (serialisiert im JSON-Format).
|TotalSize |Real | Die Gesamtgröße der Datenbank wird ausgenocht.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.

**Ausgabe für "Policies"-Option**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird die Groß-/Kleinschreibung beachtet. 
|PersistentStorage  |String |Der persistente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Die Datenbankversionsnummer. Diese Nummer wird für jeden Änderungsvorgang in der Datenbank aktualisiert (z. B. Hinzufügen von Daten und Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank diejenige ist, auf die die aktuelle Verbindung verweist. 
|DatabaseAccessMode  |String |Wie der Cluster an die Datenbank angefügt ist. Wenn die Datenbank z. B. im ReadOnly-Modus angefügt ist, schlägt der Cluster alle Anforderungen fehl, um die Datenbank in irgendeiner Weise zu ändern. 
|PrettyName |String |Der hübsche Name der Datenbank.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.
|AuthorizedPrincipals |String | Die Sammlung autorisierter Prinzipale der Datenbank (serialisiert im JSON-Format).
|RetentionPolicy |String | Die Aufbewahrungsrichtlinie der Datenbank (serialisiert im JSON-Format).
|MergePolicy |String | Die Extents Merge-Richtlinie der Datenbank (serialisiert im JSON-Format).
|CachingPolicy |String | Die Caching-Richtlinie der Datenbank (serialisiert im JSON-Format).
|Sharding-Richtlinie |String | Die Sharding-Richtlinie der Datenbank (serialisiert im JSON-Format).
|StreamingIngestionPolicy |String | Die Streaming-Aufnahmerichtlinie der Datenbank (serialisiert im JSON-Format).
|IngestionBatchingPolicy |String | Die Ingestion Batching-Richtlinie der Datenbank (serialisiert im JSON-Format).

**Ausgabe für 'datastats' Option**

|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName  |String |Der Datenbankname. Bei Datenbanknamen wird die Groß-/Kleinschreibung beachtet.
|PersistentStorage  |String |Der persistente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Die Datenbankversionsnummer. Diese Nummer wird für jeden Änderungsvorgang in der Datenbank aktualisiert (z. B. Hinzufügen von Daten und Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank diejenige ist, auf die die aktuelle Verbindung verweist. 
|DatabaseAccessMode  |String |Wie der Cluster an die Datenbank angefügt ist. Wenn die Datenbank z. B. im ReadOnly-Modus angefügt ist, schlägt der Cluster alle Anforderungen fehl, um die Datenbank in irgendeiner Weise zu ändern. 
|PrettyName |String |Der hübsche Name der Datenbank.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.
|OriginalGröße |Real | Die Gesamtgröße der Datenbank.
|ExtentSize |Real | Die Gesamtgröße der Datenbank (Daten + Indizes) der Datenbank wird erweitert.
|CompressedSize |Real | Die komprimierte Datengröße der Datenbank wird ausgegeuzt.
|IndexSize |Real | Die Gesamtindexgröße der Datenbank wird ausgegnocht.
|RowCount |Long | Die Gesamtzeilenanzahl der Datenbank wird angezeigt.
|HotOriginalSize |Real | Die Hot-Extents der Datenbank haben die ursprüngliche Gesamtgröße der Datenbank.
|HotExtentSize |Real | Die Hot-Extents der Datenbank haben die Gesamtgröße (Daten + Indizes) zu nicht zur Zeit.
|HotCompressedSize |Real | Die Hot-Extents der Datenbank haben die komprimierte Gesamtgröße der Daten.
|HotIndexSize |Real | Die Hot-Extents der Datenbank haben die Gesamtindexgröße.
|HotRowCount |Long | Die Hot-Extents der Datenbank erhöhen die Gesamtzahl der Zeilen.