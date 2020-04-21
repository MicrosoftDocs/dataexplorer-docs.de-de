---
title: Fakten- und Dimensionstabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Fakten- und Dimensionstabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 1a88f8549bf5accce197294d433ece8ccb683281
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523238"
---
# <a name="fact-and-dimension-tables"></a>Fakten- und Dimensionstabellen

Beim Entwerfen des Schemas für eine Kusto-Datenbank ist es sinnvoll, Tabellen als weit zu einer von zwei Kategorien gehörend zu betrachten:
* [Faktentabellen](https://en.wikipedia.org/wiki/Fact_table) und
* [Dimensionstabellen](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table).

**Faktentabellen** sind Tabellen, deren Datensätze unveränderliche "Fakten" sind, z. B. Dienstprotokolle und Messinformationen. Datensätze werden schrittweise (in Streaming-Manier oder in großen Blöcken) in die Tabelle eingefügt und dort aufbewahrt, bis sie aus Kostengründen entfernt werden müssen oder weil sie ihren Wert verlieren. Datensätze werden sonst nie aktualisiert.

**Dimensionstabellen** enthalten Referenzdaten (z. B. Nachschlagetabellen von einem Entitätsbezeichner zu seinen Eigenschaften) und Snapshot-ähnliche Daten (Tabellen, deren gesamter Inhalt sich in einer einzelnen Transaktion ändert). In der Regel werden solche Tabellen nicht regelmäßig mit neuen Daten aufgenommen. Stattdessen wird der gesamte Dateninhalt sofort mit Vorgängen wie [.set-or-replace](../management/data-ingestion/ingest-from-query.md), [.move extents](../management/extents-commands.md#move-extents)oder [.rename Tabellen](../management/rename-table-command.md)aktualisiert.
In einigen Fällen können Dimensionstabellen über eine [Aktualisierungsrichtlinie](../management/updatepolicy.md) für die Faktentabelle aus Faktentabellen abgeleitet werden, sowie eine Abfrage über die Tabelle, die den letzten Datensatz für jede Entität übernimmt.

Es ist wichtig zu erkennen, dass es keine Möglichkeit gibt, eine Tabelle in Kusto als Faktentabelle oder Dimensionstabelle zu "markieren". Wichtig ist, wie Daten in die Tabelle aufgenommen werden und wie die Tabellen verwendet werden.

> [!NOTE]
> Manchmal wird Kusto verwendet, um Entitätsdaten in Dertattabellen zu halten, sodass sich die Entitätsdaten langsam ändern. Beispielsweise Daten zu einer physischen Entität (z. B. einem Bürogerät), die den Standort selten ändert.
> Da Daten in Kusto unveränderlich sind, ist es üblich, dass`string`jede Tabelle zwei Spalten enthält: eine`datetime`Spalte "Identität " () , die die Entität identifiziert, und eine zuletzt geänderte ( ) Zeitstempelspalte. Dann wird nur der letzte Datensatz für jede Entitätsidentität abgerufen.



## <a name="commands-that-differentiate-fact-and-dimension-tables"></a>Befehle, die Fakten- und Dimensionstabellen unterscheiden

Es gibt Prozesse in Kusto, die zwischen Faktentabellen und Dimensionstabellen unterscheiden.

* [kontinuierlicher Export](../management/data-export/continuous-data-export.md)




Diese Prozesse werden garantiert Daten in der Tat Tabellen genau einmal verarbeiten, indem sie sich auf den [Datenbankcursormechanismus](../management/databasecursor.md) verlassen.

Beispielsweise exportiert jede Ausführung eines kontinuierlichen Exportauftrags alle Datensätze, die seit der letzten Aktualisierung des Datenbankcursors aufgenommen wurden. Daher müssen kontinuierliche Exportaufträge zwischen Faktentabellen (nur verarbeitete neu aufgenommene Daten) und Dimensionstabellen (die als Suchvorgänge verwendet werden, daher die gesamte Tabelle berücksichtigt werden) unterscheiden.