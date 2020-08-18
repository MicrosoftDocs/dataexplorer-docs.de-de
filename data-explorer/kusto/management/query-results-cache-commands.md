---
title: 'Abfrageergebnis Cache-Befehle: Azure Daten-Explorer'
description: Dieser Artikel beschreibt den Abfrageergebnis Cache in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 519560f0b6bb98651dc4a8b6749935ec9843eb9c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512570"
---
# <a name="query-results-cache-commands"></a>Abfrageergebnis Cache-Befehle

Der Abfrageergebnis Cache ist ein dedizierter Cache zum Speichern von Abfrage Ergebnissen. Weitere Informationen finden Sie unter [Abfrageergebnis Cache](../query/query-results-cache.md).

**Abfrageergebnis Cache-Befehle**

Kusto bietet zwei Befehle für die Cache Verwaltung und Observability:

* [`Show cache`](show-query-results-cache-command.md): Verwenden Sie diesen Befehl, um die vom Ergebnis Cache verfügbar gemachten Statistiken anzuzeigen.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Verwenden Sie diesen Befehl, um zwischengespeicherte Ergebnisse zu löschen.
