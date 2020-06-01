---
title: 'Externe Tabellen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden externe Tabellen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 2b03f4e0ef426b07f88d7c179caecca6e7dd3034
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257823"
---
# <a name="external-tables"></a>Externe Tabellen

Eine **externe Tabelle** ist eine Kusto-Schema Entität, die auf Daten verweist, die außerhalb der Kusto-Datenbank gespeichert

Ähnlich wie [Tabellen](tables.md)weist eine externe Tabelle ein klar definiertes Schema auf (eine geordnete Liste von Spaltenname-und Datentyp Paaren). Im Gegensatz zu Tabellen werden Daten außerhalb des Kusto-Clusters gespeichert und verwaltet. In den meisten Fällen werden die Daten in einem Standardformat, z. b. CSV, Parkett und Avro, gespeichert und nicht von Kusto erfasst.

Eine **externe Tabelle** wird einmal erstellt (siehe [Allgemeine Steuerungsbefehle für externe Tabellen](../../management/externaltables.md), [Erstellen und ändern externer SQL-Tabellen](../../management/external-sql-tables.md)und [Erstellen und Ändern von Tabellen in Azure Storage oder Azure Data Lake](../../management/external-tables-azurestorage-azuredatalake.md)). der Name kann mithilfe der [external_table ()](../../query/externaltablefunction.md) -Funktion mit dem Namen referenziert werden. 

**Hinweise**

* Bei externen Tabellennamen wird Groß-/Kleinschreibung beachtet.
* Externe Tabellennamen dürfen sich nicht mit den Kusto-Tabellennamen überlappen.
* Externe Tabellennamen entsprechen den Regeln für [Entitäts Namen](./entity-names.md).
* Der maximale Grenzwert externer Tabellen pro Datenbank beträgt 1.000.
* Kusto unterstützt den [Export](../../management/data-export/export-data-to-an-external-table.md) und den [fortlaufenden Export](../../management/data-export/continuous-data-export.md) in eine externe Tabelle sowie das [Abfragen externer Tabellen](../../../data-lake-query-data.md).
    * Das Löschen von [Daten](../../concepts/data-purge.md) wird nicht auf externe Tabellen angewendet. Datensätze werden nie aus externen Tabellen gelöscht.
