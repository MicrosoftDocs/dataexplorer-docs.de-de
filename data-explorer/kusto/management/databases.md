---
title: 'Datenbankverwaltung: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die Verwaltung von Datenbanken in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 9dc2e1bf489123ddd66c9a203d74284e36eda53e
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320943"
---
# <a name="databases-management"></a>Datenbankverwaltung

In diesem Thema werden die folgenden Daten Bank Steuerungsbefehle beschrieben:

|Befehl |Beschreibung |
|--------|------------|
|[`.show databases`](show-databases.md) |Gibt eine Tabelle zurück, in der jeder Datensatz einer Datenbank im Cluster entspricht, auf die der Benutzer Zugriff hat.|
|[`.show database`](show-database.md) |Gibt eine Tabelle zurück, in der die Eigenschaften der Kontext Datenbank angezeigt werden. |
|[`.show cluster databases`](show-cluster-database.md) |Gibt eine Tabelle mit allen Datenbanken zurück, die mit dem Cluster verbunden sind und für die der Benutzer, der den Befehl aufruft, Zugriff hat |
|[`.alter database`](alter-database.md) |Ändert den hübschen (freundlichen) Namen einer Datenbank. |
|[`.show database schema`](show-schema-database.md) |Gibt eine flache Liste der Struktur der ausgewählten Datenbanken mit allen Tabellen und Spalten in einer einzelnen Tabelle oder einem JSON-Objekt zurück. |