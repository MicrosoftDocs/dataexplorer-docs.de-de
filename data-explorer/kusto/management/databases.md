---
title: Datenbankverwaltung - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die Datenbankverwaltung in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 78fcba2db059c0115d65032610009ab20bd2d5bd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521283"
---
# <a name="databases-management"></a>Datenbankverwaltung

In diesem Thema werden die folgenden Datenbanksteuerungsbefehle beschrieben:

|Get-Help |BESCHREIBUNG |
|--------|------------|
|[.Datenbanken anzeigen](show-databases.md) |Gibt eine Tabelle zurück, in der jeder Datensatz einer Datenbank im Cluster entspricht, auf die der Benutzer Zugriff hat|
|[.show-Datenbank](show-database.md) |Gibt eine Tabelle mit den Eigenschaften der Kontextdatenbank zurück |
|[.Show Cluster-Datenbanken](show-cluster-database.md) |Gibt eine Tabelle zurück, in der alle Datenbanken angezeigt werden, die an den Cluster angefügt sind und auf die der Benutzer, der den Befehl aufruft, Zugriff hat |
|[.alter-Datenbank](alter-database.md) |Ändert den hübschen (freundlichen) Namen einer Datenbank |
|[.drop-Datenbank](drop-database.md) |Löscht den hübschen (freundlichen) Namen einer Datenbank |
|[.show Datenbankschema](show-schema-database.md) |Gibt eine flache Liste der Struktur der ausgewählten Datenbanken mit allen Tabellen und Spalten in einer einzelnen Tabelle oder einem JSON-Objekt zurück |