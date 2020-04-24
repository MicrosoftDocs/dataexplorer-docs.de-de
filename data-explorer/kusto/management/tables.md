---
title: Tabellenverwaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Tabellenverwaltung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 7b7e4c5c7111354864aa939ece76be2ab0a8ac15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519566"
---
# <a name="tables-management"></a>Tabellenverwaltung

In diesem Thema wird der Lebenszyklus von Tabellen und zugehörigen Steuerbefehlen behandelt.

Wählen Sie die Links in der folgenden Tabelle aus, um weitere Informationen zu erhalten.

| Befehle                                                                                                                 | Vorgang                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | Verwalten von Tabellenanzeigeeigenschaften |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | Verwalten der Aufnahmezuordnung        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | Erstellen/Ändern/Ablegen von Tabellen       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | Aufzählen von Tabellen in einer Datenbank  |
| `.ingest`, `.set` `.append`, `.set-or-append` (Details finden Sie unter [Datenerfassung).)](./data-ingestion/index.md)                                                                                                                                                                                      | Datenerfassung in einer Tabelle     |

## <a name="crud-naming-conventions-for-tables"></a>CRUD-Namenskonventionen für Tabellen 
(Siehe alle Details in den Abschnitten, die in der Tabelle weiter unten verlinkt sind.)
 
| Befehlssyntax                             | Semantik                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | Wenn eine Entität dieses Typs und namens vorhanden ist, gibt die Entität zurück. Andernfalls erstellen Sie die Entität.                          |
| `.create-merge entityType entityName...`   | Wenn eine Entität dieses Typs und namens vorhanden ist, führen Sie die vorhandene Entität mit der angegebenen Entität zusammen. Andernfalls erstellen Sie die Entität. |
| `.alter entityType entityName ...`         | Wenn eine Entität dieses Typs und namens nicht vorhanden ist, Fehler. Andernfalls ersetzen Sie sie durch die angegebene Entität.            |
| `.alter-merge entityType entityName ...`   | Wenn eine Entität dieses Typs und namens nicht vorhanden ist, Fehler. Andernfalls führen Sie sie mit der angegebenen Entität zusammen.              |
| `.drop entityType entityName ...`          | Wenn eine Entität dieses Typs und namens nicht vorhanden ist, Fehler. Andernfalls lassen Sie es fallen.                                         |
| `.drop entityType entityName ifexists ...` | Wenn eine Entität dieses Typs und namens nicht vorhanden ist, geben Sie zurück. Andernfalls lassen Sie es fallen.                                        |
 
> [!NOTE]
> "Zusammenführen" ist eine logische Zusammenführung zweier Entitäten:
>
> * Wenn eine Eigenschaft für eine Entität, aber nicht für die andere Entität definiert ist, wird sie mit ihrem ursprünglichen Wert in der zusammengeführten Entität angezeigt.
> * Wenn eine Eigenschaft für beide Entitäten definiert ist und in beiden Entitäten den gleichen Wert hat, wird sie einmal mit diesem Wert in der zusammengeführten Entität angezeigt.
> * Wenn eine Eigenschaft für beide Entitäten definiert ist, aber unterschiedliche Werte aufweist, wird ein Fehler ausgelöst.