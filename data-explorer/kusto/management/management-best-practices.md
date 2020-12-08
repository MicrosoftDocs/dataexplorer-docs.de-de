---
title: 'Bewährte Methoden für die Schema Verwaltung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt bewährte Methoden für die Schema Verwaltung in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: ba0d931fcca257cb3b5658354dfb8bf70dc6848f
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774621"
---
# <a name="best-practices-for-schema-management"></a>Bewährte Methoden für die Schema Verwaltung

Es folgen einige bewährte Methoden, die befolgt werden müssen. Sie unterstützen Sie dabei, die Funktionsweise ihrer Verwaltungs Befehle zu verbessern und die Dienst Ressourcen leichter zu beeinträchtigen.

|Aktion  |Verwendung  |Nicht verwenden | Notizen |
|---------|---------|---------|----
| **Erstellen mehrerer Tabellen**    |  Verwenden eines einzelnen [`.create tables`](create-tables-command.md) Befehls       | Viele Befehle nicht ausgeben `.create table`        | |
| **Umbenennen mehrerer Tabellen**    | Erstellen Sie einen einzelnen-Befehl. [`.rename tables`](rename-table-command.md)        |  Geben Sie für jedes Tabellen Paar keinen separaten-Befehl aus.   |    |
|**Befehle anzeigen**   |   Verwenden Sie den Befehl mit dem niedrigsten Bereich. `.show` |   Filter nicht nach einer Pipe ( `|` ) anwenden   </ul></li>  | Beschränken Sie die Verwendung so weit wie möglich. Speichern Sie nach Möglichkeit die Informationen zwischen, die Sie zurückgeben. |
| Blöcke anzeigen  | Verwenden Sie `.show table T extents`   |Nicht verwenden `.show cluster extents | where TableName == 'T'`  |
|  Datenbankschema anzeigen. |Verwenden Sie `.show database DB schema`  |  Nicht verwenden `.show schema | where DatabaseName == 'DB'` |
| **Anzeigen eines Schemas in einem Cluster mit einem großen Schema** <br> |Konsum [`.show databases schema`](../management/show-schema-database.md) |Nicht verwenden `.show schema`| Verwenden Sie z. b. für Cluster mit mehr als 100 Datenbanken.
| **Das vorhanden sein einer Tabelle überprüfen oder das Schema der Tabelle erhalten**|Verwenden Sie `.show table T schema as json`|Nicht verwenden  `.show table T` |Verwenden Sie diesen Befehl nur, um tatsächliche Statistiken für eine einzelne Tabelle zu erhalten.|
| **Definieren des Schemas für eine Tabelle, die `datetime` Werte enthält**  |Legen Sie die relevanten Spalten auf den Typ fest. `datetime` | Konvertieren `string` Sie oder numerische Spalten nicht zum `datetime` Zeitpunkt der Abfrage zum Filtern, wenn dies vor oder während der Erfassungs Zeit erfolgen kann.|
| **Hinzufügen eines Block Tags zu Metadaten** |Sparsam verwenden |Vermeiden `drop-by:` Sie Tags, die die Fähigkeit des Systems einschränken, leistungsorientierte Bereinigungs Prozesse im Hintergrund durchzuführen.|  <br> Siehe [Leistungs Hinweise](../management/extents-overview.md#extent-tagging). |
