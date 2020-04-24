---
title: Befehls- und Abfrageverwaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Befehls- und Abfrageverwaltung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: f8c01709d69a0b439ce51b4782eb8f747db15d65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521912"
---
# <a name="commands-and-queries-management"></a>Befehls- und Abfrageverwaltung

## <a name="show-commands-and-queries"></a>.show Befehle und Abfragen 

`.show``commands-and-queries` gibt eine Tabelle mit Admin-Befehlen und Abfragen zurück, die einen endgültigen Status erreicht haben. Diese Befehle und Abfragen können 30 Tage lang abgefragt werden.

Die in der Ausgabe des Befehls dargestellten Informationen ähneln denen, die von [.show-Befehlen](commands.md) und [.show-Abfragen](queries.md)dargestellt werden, aber es erlaubt im Wesentlichen, beide Ergebnissätze auf einfache Weise zu vereinigen.

**Syntax**

`.show` `commands-and-queries`
 
**Ausgabe**
 
Das Ausgabeschema ist wie folgt:

| ColumnName               | ColumnType |
|--------------------------|------------|
| ClientActivityId         | Zeichenfolge     |
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
| Application              | Zeichenfolge     |
| Prinzipal                | Zeichenfolge     |
| ClientRequestProperties  | dynamisch    |
| TotalCpu                 | Zeitraum   |
| MemoryPeak               | long       |
| CacheStatistiken          | dynamisch    |
| GescannteExtentsStatistiken | dynamisch    |
| ResultSetStatistiken      | dynamisch    |

Beachten Sie, dass bei `CommandType` `Query`Abfragen der Wert von .