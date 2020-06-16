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
ms.openlocfilehash: a76ef031a3fbcaa26f90fd2c2664920805b823b5
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780268"
---
# <a name="external-tables"></a>Externe Tabellen

Eine **externe Tabelle** ist eine Kusto-Schema Entität, die auf Daten verweist, die außerhalb der Azure Daten-Explorer-Datenbank gespeichert sind.

Ähnlich wie [Tabellen](tables.md)weist eine externe Tabelle ein klar definiertes Schema auf (eine geordnete Liste von Spaltenname-und Datentyp Paaren). Im Gegensatz zu Tabellen werden Daten außerhalb des Clusters gespeichert und verwaltet. In den meisten Fällen werden die Daten in einem Standardformat wie CSV, Parkett und Avro gespeichert und nicht von Azure Daten-Explorer erfasst.

Eine **externe Tabelle** wird einmal erstellt. Sehen Sie sich die folgenden Befehle für die Erstellung externer Tabellen an:
* [Externe Tabelle (allgemeine Steuerungsbefehle)](../../management/externaltables.md)
* [Erstellen und Ändern externer SQL-Tabellen](../../management/external-sql-tables.md)
* [Erstellen und Ändern von Tabellen in Azure Storage oder Azure Data Lake](../../management/external-tables-azurestorage-azuredatalake.md)

Auf eine **externe Tabelle** kann mit dem Namen mithilfe der [external_table ()](../../query/externaltablefunction.md) -Funktion verwiesen werden. 

**Notizen**

* Externe Tabellennamen:
   * Groß-/Kleinschreibung wird beachtet,
   * Kann nicht mit den Namen von Kusto-Tabellen überlappend
   * Befolgen Sie die Regeln für [Entitäts Namen](./entity-names.md).
* Der maximale Grenzwert externer Tabellen pro Datenbank beträgt 1.000.
* Kusto unterstützt den [Export](../../management/data-export/export-data-to-an-external-table.md) und den [fortlaufenden Export](../../management/data-export/continuous-data-export.md) in eine externe Tabelle sowie das [Abfragen externer Tabellen](../../../data-lake-query-data.md).
* Das Löschen von [Daten](../../concepts/data-purge.md) wird nicht auf externe Tabellen angewendet. Datensätze werden nie aus externen Tabellen gelöscht.
