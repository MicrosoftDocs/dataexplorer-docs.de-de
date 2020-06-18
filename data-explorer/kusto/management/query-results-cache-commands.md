---
title: 'Abfrageergebnis Cache: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Abfrageergebnis Cache in Azure Daten-Explorer.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 630266fdaaaac51037a99a6a5243db58a232ae48
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943077"
---
# <a name="query-results-cache"></a>Abfrageergebniscache

Der Abfrageergebnis Cache ist ein dedizierter Cache zum Speichern von Abfrage Ergebnissen. Weitere Informationen finden Sie unter [Abfrageergebnis Cache](../query/query-results-cache.md).

**Abfrageergebnis Cache-Befehle**

Kusto bietet zwei Befehle für die Cache Verwaltung und Observability:

* [`Show cache`](show-query-results-cache-command.md): Verwenden Sie diesen Befehl, um die vom Ergebnis Cache verfügbar gemachten Statistiken anzuzeigen.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Verwenden Sie diesen Befehl, um zwischengespeicherte Ergebnisse zu löschen.
