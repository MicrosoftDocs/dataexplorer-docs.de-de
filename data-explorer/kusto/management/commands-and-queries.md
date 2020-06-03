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
ms.openlocfilehash: c7f692739071496ce492d168c6036a2c2adac8fd
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329043"
---
# <a name="commands-and-queries-management"></a>Verwaltung von Befehlen und Abfragen

## <a name="show-commands-and-queries"></a>. Befehle anzeigen-und-Abfragen 

`.show``commands-and-queries`gibt eine Tabelle mit admin-Befehlen und Abfragen zurück, die einen Endzustand erreicht haben. Diese Befehle und Abfragen sind 30 Tage lang verfügbar.

Die Informationen, die in der Ausgabe des Befehls angezeigt werden, ähneln [. Show-Befehle](commands.md) und [. Show-Abfragen](queries.md), Sie können jedoch im Grunde beide Resultsets auf einfache Weise verknüpfen.

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
| Startedon                | datetime   |
| Lastupdatedon            | datetime   |
| Duration                 | Zeitraum   |
| Staat                    | Zeichenfolge     |
| FailureReason            | Zeichenfolge     |
| RootActivityId           | guid       |
| Benutzer                     | Zeichenfolge     |
| Application              | Zeichenfolge     |
| Prinzipal                | Zeichenfolge     |
| Clientrequestproperties  | dynamisch    |
| Totalcpu                 | Zeitraum   |
| Memorypeak               | long       |
| CacheStatistics          | dynamisch    |
| Scannetdextentsstatistics | dynamisch    |
| Resultsetstatistics      | dynamisch    |

> [!NOTE]
> Für-Abfragen ist der Wert `CommandType` von `Query` .
