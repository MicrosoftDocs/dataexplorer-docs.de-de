---
title: 'Tabellen Verwaltung: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Verwaltung von Tabellen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 4de0e749ad47b8f2e3f2c0f26d5d18466efaff97
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967297"
---
# <a name="tables-management"></a>Tabellen Verwaltung

In diesem Thema wird der Lebenszyklus von Tabellen und zugehörigen Steuerungs Befehlen erläutert, die zum Durchsuchen, erstellen und Ändern von Tabellen hilfreich sind.

Wählen Sie in der Tabelle unten die Links aus, um weitere Informationen zu diesen zu finden.

| Befehle                                                                                                                 | Vorgang                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | Tabellen Anzeigeeigenschaften verwalten |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | Erfassungs Zuordnung verwalten        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | Erstellen/Ändern/Löschen von Tabellen       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | Auflisten von Tabellen in einer Datenbank  |
| `.ingest`, `.set` , `.append` , `.set-or-append` (Weitere Informationen finden Sie unter [Daten](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) Erfassung).)                                                                                                                                                                                      | Erfassung von Daten in eine Tabelle     |

## <a name="crud-naming-conventions-for-tables"></a>CRUD-Benennungs Konventionen für Tabellen 
(Ausführliche Informationen finden Sie in den Abschnitten, die in der obigen Tabelle mit verknüpft sind.)
 
| Befehlssyntax                             | Semantik                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | Gibt die Entität zurück, wenn eine Entität dieses Typs und Namens vorhanden ist. Andernfalls erstellen Sie die Entität.                          |
| `.create-merge entityType entityName...`   | Wenn eine Entität dieses Typs und Namens vorhanden ist, führen Sie die vorhandene Entität mit der angegebenen Entität zusammen. Andernfalls erstellen Sie die Entität. |
| `.alter entityType entityName ...`         | Wenn keine Entität dieses Typs und Namens vorhanden ist, tritt ein Fehler auf. Andernfalls ersetzen Sie Sie durch die angegebene Entität.            |
| `.alter-merge entityType entityName ...`   | Wenn keine Entität dieses Typs und Namens vorhanden ist, tritt ein Fehler auf. Andernfalls führen Sie die Zusammenführung mit der angegebenen Entität aus.              |
| `.drop entityType entityName ...`          | Wenn keine Entität dieses Typs und Namens vorhanden ist, tritt ein Fehler auf. Andernfalls können Sie es löschen.                                         |
| `.drop entityType entityName ifexists ...` | Wenn keine Entität dieses Typs und Namens vorhanden ist, geben Sie zurück. Andernfalls können Sie es löschen.                                        |
 
> [!NOTE]
> "Merge" ist eine logische Zusammenführung von zwei Entitäten:
>
> * Wenn eine Eigenschaft für eine Entität, aber nicht für die andere definiert ist, wird Sie mit Ihrem ursprünglichen Wert in der zusammengeführten Entität angezeigt.
> * Wenn eine Eigenschaft für beide Entitäten definiert ist und in beiden denselben Wert aufweist, wird Sie einmal mit diesem Wert in der zusammengeführten Entität angezeigt.
> * Wenn eine Eigenschaft für beide Entitäten definiert ist, aber unterschiedliche Werte aufweist, wird ein Fehler ausgelöst.