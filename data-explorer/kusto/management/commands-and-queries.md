---
title: 'Verwaltung von Befehlen und Abfragen: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Verwaltung von Befehlen und Abfragen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 222d04939560342bd849c15f249b1a8a582316a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321028"
---
# <a name="commands-and-queries-management"></a>Verwaltung von Befehlen und Abfragen

## <a name="show-commands-and-queries"></a>. Befehle anzeigen-und-Abfragen 

`.show``commands-and-queries`gibt eine Tabelle mit admin-Befehlen und Abfragen zurück, die einen Endzustand erreicht haben. Diese Befehle und Abfragen sind 30 Tage lang verfügbar.

Die in der Ausgabe des Befehls dargestellten Informationen ähneln [ `.show` Befehlen](commands.md) und [ `.show` Abfragen](queries.md), Sie ermöglichen Ihnen jedoch im Grunde, beide Resultsets auf einfache Weise zu verknüpfen.

**Syntax**

`.show` `commands-and-queries`
 
**Ausgabe**
 
Das Ausgabe Schema lautet wie folgt:

| ColumnName               | ColumnType |
|--------------------------|------------|
| Clientactivityid         | Zeichenfolge     |
| CommandType              | Zeichenfolge     |
| Text                     | Zeichenfolge     |
| Datenbank                 | Zeichenfolge     |
| StartedOn                | datetime   |
| LastUpdatedOn            | datetime   |
| Duration                 | Zeitraum   |
| State                    | Zeichenfolge     |
| FailureReason            | Zeichenfolge     |
| RootActivityId           | guid       |
| Benutzer                     | Zeichenfolge     |
| Anwendung              | Zeichenfolge     |
| Prinzipal                | Zeichenfolge     |
| Clientrequestproperties  | dynamisch    |
| TotalCpu                 | Zeitraum   |
| MemoryPeak               | long       |
| CacheStatistics          | dynamisch    |
| ScannedExtentsStatistics | dynamisch    |
| ResultSetStatistics      | dynamisch    |

> [!NOTE]
> Für-Abfragen ist der Wert `CommandType` von `Query` .
