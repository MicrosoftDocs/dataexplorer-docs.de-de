---
title: 'Abfrageergebnis Cache: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Abfrageergebnis Cache in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: fa2bf2f6d24162c5bdb1c851ef7d74e4eb39489f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349906"
---
# <a name="query-results-cache"></a>Abfrageergebniscache

Der Abfrageergebnis Cache ist ein dedizierter Cache zum Speichern von Abfrage Ergebnissen. Weitere Informationen finden Sie unter [Abfrageergebnis Cache](../query/query-results-cache.md).

**Abfrageergebnis Cache-Befehle**

Kusto bietet zwei Befehle für die Cache Verwaltung und Observability:

* [`Show cache`](show-query-results-cache-command.md): Verwenden Sie diesen Befehl, um die vom Ergebnis Cache verfügbar gemachten Statistiken anzuzeigen.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Verwenden Sie diesen Befehl, um zwischengespeicherte Ergebnisse zu löschen.
