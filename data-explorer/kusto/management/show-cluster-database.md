---
title: '. Anzeigen von Cluster Datenbanken: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie Cluster Datenbanken in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c6d25380a44a2195f407c52a2224ee28fad9f8bb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320501"
---
# <a name="show-cluster-databases"></a>.show cluster databases

Gibt eine Tabelle zurück, die alle an den Cluster angefügten Datenbanken anzeigt und auf die der Benutzer, der den Befehl aufruft, Zugriff hat. Wenn bestimmte Datenbanknamen verwendet werden, sind nur diese Datenbanken enthalten.

**Syntax**

`.show` `cluster` `databases` [`details` | `identity` | `policies` | `datastats`]

`.show``cluster` `databases` `(` Database1 `,` Database2 `,` ... Databasen`)`

**Ausgabe**
 
|Ausgabeparameter |type |Beschreibung 
|---|---|---
|DatabaseName  |String |Datenbankname. Bei Datenbanknamen wird Groß-/Kleinschreibung beachtet. 
|Persistentstorage  |String |Der permanente Speicher-URI, in dem die Datenbank gespeichert ist. (Dieses Feld ist für kurzlebige Datenbanken leer.) 
|Version  |String |Versionsnummer der Datenbank. Diese Nummer wird für jeden Änderungs Vorgang in der Datenbank aktualisiert (z. b. das Hinzufügen von Daten und das Ändern des Schemas). 
|IsCurrent  |Boolean |True, wenn die Datenbank die Datenbank ist, auf die die aktuelle Verbindung verweist. 
|Databaseaccessmode  |String |Gibt an, wie der Cluster an die Datenbank angefügt wird. Wenn die Datenbank z. b. im schreibgeschützten Modus angefügt ist, misslingt der Cluster alle Anforderungen, um die Datenbank zu ändern. 
|Prettyname |String |Der Name der Datenbank ist ziemlich.
|Currentuserisunrestrictedviewer |Boolean | Gibt an, ob der aktuelle Benutzer ein uneingeschränkter Viewer für die Datenbank ist.
|Datenbank-ID (DatabaseId) |Guid |Die eindeutige ID der Datenbank.