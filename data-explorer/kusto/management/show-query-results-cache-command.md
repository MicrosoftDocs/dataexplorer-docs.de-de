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
ms.openlocfilehash: 7b7a96a01a4ec2b6c84609b2f9c518637d174390
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349889"
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
