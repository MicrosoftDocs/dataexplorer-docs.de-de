---
title: Externe Tabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden externe Tabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f4a059ba4533ed91353e75b499e564e6dd06d591
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509383"
---
# <a name="external-tables"></a>Externe Tabellen

Eine **externe Tabelle** ist eine Kusto-Schemaentität, die auf Daten verweist, die außerhalb der Kusto-Datenbank gespeichert sind.

Ähnlich wie [Tabellen](tables.md)verfügt eine externe Tabelle über ein genau definiertes Schema (eine geordnete Liste von Spaltennamen und Datentyppaaren). Im Gegensatz zu Tabellen werden Daten außerhalb von Kusto Cluster gespeichert und verwaltet. Am häufigsten werden die Daten in einem Standardformat wie CSV, Parkett, Avro gespeichert und nicht von Kusto aufgenommen.

Eine **externe Tabelle** wird einmal erstellt (siehe [Steuerelementbefehle für externe Tabellen](../../management/externaltables.md)) und kann mithilfe der Funktion [external_table()](../../query/externaltablefunction.md) durch ihren Namen referenziert werden. 

**Hinweise**

* Bei externen Tabellennamen wird die Groß-/Kleinschreibung beachtet.
* Externe Tabellennamen können sich nicht mit Kusto-Tabellennamen überlappen.
* Externe Tabellennamen folgen den Regeln für [Entitätsnamen](./entity-names.md).
* Die maximale Anzahl externer Tabellen pro Datenbank beträgt 1.000.
* Kusto unterstützt das [Exportieren von Daten in eine externe Tabelle](../../management/data-export/export-data-to-an-external-table.md) sowie das Abfragen [externer Tabellen](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data).
