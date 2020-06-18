---
title: 'Abfrageergebnis Cache anzeigen: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie den Abfrageergebnis Cache in Azure Daten-Explorer anzeigen.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: bcbdb59355ce0461d735cbea902551c219479fd2
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943074"
---
# <a name="show-query-results-cache"></a>. Abfrageergebnis Cache anzeigen

Gibt eine Tabelle zurück, in der Statistiken zum [Abfrageergebnis Cache](../query/query-results-cache.md)angezeigt werden.

**Syntax**

`.show` `query` `results` `cache`

**Ausgabe**
 
|Output-Parameter |type |Beschreibung 
|---|---|---
|Treffer  |long |Die Anzahl der Cache Treffer.
|Cachefehler  |long |Die Anzahl der Cache Fehler.
|Cachecapacityinbytes |long |Die Cache Kapazität in Byte.
|Usedbytes  |long |Der Cache verwendete Speicherplatz.
|Anzahl  |long | Die Anzahl der im Cache gespeicherten eindeutigen Abfrageergebnisse.

**Einschränkungen**

* Die Ausgabe des Befehls gibt derzeit nur die Cache Statistik wieder, die von dem Knoten erfasst wurde, auf dem die Anforderung gelandet ist.
* Mit dem Befehl wird nur der aktuelle Verlauf angezeigt.
