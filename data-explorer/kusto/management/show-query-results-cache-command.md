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
ms.openlocfilehash: 84805cae07049af4ffa8a2fdb82e637261140f8f
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565424"
---
# <a name="show-database-cache-query_results"></a>. Daten Bank Cache query_results anzeigen

Gibt eine Tabelle mit Statistiken zu dem [Abfrageergebnis Cache](../query/query-results-cache.md) zurück, der für die Kontext Datenbank erstellt wurde.

**Syntax**

`.show database query results cache`

**Ausgabe**
 
|Output-Parameter |type |BESCHREIBUNG 
|---|---|---
|NodeId|`string`|Der Bezeichner des Cluster Knotens.
|Treffer  |`long`|Die Anzahl der Cache Treffer.
|Cachefehler  |`long`|Die Anzahl der Cache Fehler.
|Cachecapacityinbytes |`long` |Die Cache Kapazität in Byte.
|Usedbytes  |`long` |Der Cache verwendete Speicherplatz.
|Anzahl  |`long`| Die Anzahl der im Cache gespeicherten eindeutigen Abfrageergebnisse.
