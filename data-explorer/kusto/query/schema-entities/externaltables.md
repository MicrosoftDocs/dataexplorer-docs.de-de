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
ms.openlocfilehash: 7f74732ed38d0b41a857fc549f549ce54ad4dce6
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863709"
---
# <a name="external-tables"></a>Externe Tabellen

Eine **externe Tabelle** ist eine Kusto-Schema Entität, die auf Daten verweist, die außerhalb der Kusto-Datenbank gespeichert

Ähnlich wie [Tabellen](tables.md)weist eine externe Tabelle ein klar definiertes Schema auf (eine geordnete Liste von Spaltenname-und Datentyp Paaren). Im Gegensatz zu Tabellen werden Daten außerhalb des Kusto-Clusters gespeichert und verwaltet. In den meisten Fällen werden die Daten in einem Standardformat, z. b. CSV, Parkett und Avro, gespeichert und nicht von Kusto erfasst.

Eine **externe Tabelle** wird einmal erstellt (siehe [Allgemeine Steuerungsbefehle für externe Tabellen](../../management/externaltables.md), [Erstellen und ändern externer SQL-Tabellen](../../management/external-sql-tables.md)und [Erstellen und Ändern von Tabellen im Speicher](../../management/external-tables-azurestorage-azuredatalake.md)). auf Sie kann mit dem Namen mithilfe der [external_table ()](../../query/externaltablefunction.md) -Funktion verwiesen werden. 

**Hinweise**

* Bei externen Tabellennamen wird Groß-/Kleinschreibung beachtet.
* Externe Tabellennamen dürfen sich nicht mit den Kusto-Tabellennamen überlappen.
* Externe Tabellennamen entsprechen den Regeln für [Entitäts Namen](./entity-names.md).
* Der maximale Grenzwert externer Tabellen pro Datenbank beträgt 1.000.
* Kusto unterstützt das [Exportieren von Daten in eine externe Tabelle](../../management/data-export/export-data-to-an-external-table.md) sowie das [Abfragen externer Tabellen](../../../data-lake-query-data.md).
