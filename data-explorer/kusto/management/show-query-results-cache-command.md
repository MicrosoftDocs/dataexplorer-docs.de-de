---
title: 'Abfrageergebnis Cache anzeigen: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie den Abfrageergebnis Cache in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: e037b6ed01a2eba7c7370d75a4efea0f7cbc1756
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402717"
---
# <a name="show-database-cache-query_results"></a>. Daten Bank Cache query_results anzeigen

Gibt eine Tabelle mit Statistiken zu dem [Abfrageergebnis Cache](../query/query-results-cache.md) zurück, der für die Kontext Datenbank erstellt wurde.

**Syntax**

`.show database cache query_results`

**Ausgabe**
 
|Ausgabeparameter |type |BESCHREIBUNG 
|---|---|---
|NodeId|`string`|Der Bezeichner des Cluster Knotens.
|Treffer  |`long`|Die Anzahl der Cache Treffer.
|Cachefehler  |`long`|Die Anzahl der Cache Fehler.
|Cachecapacityinbytes |`long` |Die Cache Kapazität in Byte.
|Usedbytes  |`long` |Der Cache verwendete Speicherplatz.
|Anzahl  |`long`| Die Anzahl der im Cache gespeicherten eindeutigen Abfrageergebnisse.
