---
title: materialized_view () (Scope-Funktion)-Azure Daten-Explorer
description: In diesem Artikel wird materialized_view ()-Funktion in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: e8dee5e1c33328eb70f984b20f61d4585abae550
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057126"
---
# <a name="materialized_view-function"></a>materialized_view ()-Funktion

Verweist auf den materialisierten Teil einer [materialisierten Sicht](../management/materialized-views/materialized-view-overview.md). 

Die- `materialized_view()` Funktion unterstützt eine Methode zum Abfragen des *materialisierten* Teils nur der Sicht und gibt dabei die maximale Latenz an, die der Benutzer tolerieren soll. Diese Option stellt nicht sicher, dass die aktuellsten Datensätze zurückgegeben werden, sondern sollte immer leistungsfähiger sein als das Abfragen der gesamten Sicht. Diese Funktion ist nützlich für Szenarien, in denen Sie bereit sind, eine gewisse Aktualität der Leistung zu Opfern, z. b. in telemetriedashboards.

<!--- csl --->
```
materialized_view('ViewName')
```

## <a name="syntax"></a>Syntax

`materialized_view(`*ViewName* `,` [*max_age*]`)`

## <a name="arguments"></a>Argumente

* *ViewName*: der Name von `materialized view` .
* *max_age*: optional. Wenn nicht angegeben, wird nur der *materialisierte* Teil der Sicht zurückgegeben. Wenn angegeben, gibt die Funktion den _materialisierten_ Teil der Sicht zurück, wenn die letzte Materialisierungs Zeit größer als [ @now -max_age] ist. Andernfalls wird die gesamte Ansicht zurückgegeben (identisch mit der direkten Abfrage von *viewName* ). 

## <a name="examples"></a>Beispiele

Abfragen des *materialisierten* Teils der Sicht, unabhängig vom Zeitpunkt der letzten Materialisierung.

<!-- csl -->
```
materialized_view("ViewName")
```

Fragen Sie den *materialisierten* Teil nur ab, wenn er in den letzten 10 Minuten materialisiert wurde. Wenn der materialisierte Teil älter als 10 Minuten ist, müssen Sie die vollständige Ansicht zurückgeben. Diese Option ist weniger leistungsfähig als das Abfragen des materialisierten Teils.

<!-- csl -->
```
materialized_view("ViewName", 10m)
```

## <a name="notes"></a>Hinweise

* Nachdem eine Sicht erstellt wurde, kann Sie genauso wie jede andere Tabelle in der Datenbank abgefragt werden, einschließlich der Teil-und datenbankübergreifenden Abfragen.
* Materialisierte Sichten sind nicht in Platzhalter-Unions oder-suchen enthalten.
* Die Syntax zum Abfragen der Sicht ist der Ansichts Name (z. b. ein Tabellen Verweis).
* Wenn Sie die materialisierte Sicht Abfragen, werden immer die aktuellsten Ergebnisse zurückgegeben, basierend auf allen Datensätzen, die in der Quell Tabelle erfasst wurden. Die Abfrage kombiniert den materialisierten Teil der Sicht mit allen nicht materialisierten Datensätzen in der Quell Tabelle. Weitere Informationen finden Sie [im Hintergrund im Hintergrund](../management/materialized-views/materialized-view-overview.md#how-materialized-views-work) .
