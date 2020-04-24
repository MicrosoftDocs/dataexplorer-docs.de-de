---
title: .Anzeigen von Clusterdatenbanken - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Clusterdatenbanken in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f354862df1bc9bef352819832125cf6f82ba0ae4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519991"
---
# <a name="show-cluster-databases"></a>.Show Cluster-Datenbanken

Gibt eine Tabelle zurück, in der alle Datenbanken angezeigt werden, die an den Cluster angefügt sind und auf die der Benutzer, der den Befehl aufruft, Zugriff hat. Wenn bestimmte Datenbanknamen verwendet werden, werden nur diese Datenbanken einbezogen.

**Syntax**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `,` Datenbank1`,` Datenbank2 ... `databases` `(` databaseN`)`

**Ausgabe**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|DatabaseName  |String |Datenbankname. Bei Datenbanknamen wird die Groß-/Kleinschreibung beachtet. 
|PersistentStorage  |String |Der persistente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Versionsnummer der Datenbank. Diese Nummer wird für jeden Änderungsvorgang in der Datenbank aktualisiert (z. B. Hinzufügen von Daten und Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank diejenige ist, auf die die aktuelle Verbindung verweist. 
|DatabaseAccessMode  |String |Wie der Cluster an die Datenbank angefügt ist. Wenn die Datenbank z. B. im ReadOnly-Modus angefügt ist, schlägt der Cluster alle Anforderungen zum Ändern der Datenbank in irgendeiner Weise fehl. 
|PrettyName |String |Die Datenbank hübscher Name.
|CurrentUserIsUnrestrictedViewer |Boolean | Gibt an, ob der aktuelle Benutzer ein uneingeschränkter Viewer in der Datenbank ist.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.