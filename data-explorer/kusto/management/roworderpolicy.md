---
title: Zeilenreihenfolgerichtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Zeilenreihenfolgerichtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 4dca1a4083a6141eb94d9bf3cf69f7134ebfca04
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520212"
---
# <a name="row-order-policy"></a>Richtlinie für die Zeilenreihenfolge

Die Zeilenreihenfolgerichtlinie ist eine optionale Richtlinie, die für Tabellen festgelegt ist und Kusto einen "Hinweis" auf die gewünschte Reihenfolge von Zeilen in einem Datenshard bietet.

Der Hauptzweck der Richtlinie besteht nicht darin, die Komprimierung zu verbessern (obwohl es sich um einen potenziellen Nebeneffekt handelt), sondern die Leistung von Abfragen zu verbessern, von denen bekannt ist, dass sie auf eine kleine Teilmenge von Werten in den geordneten Spalten eingegrenzt sind.

Die Anwendung der Richtlinie ist sinnvoll, wenn:
* Die meisten Abfragen filtern nach bestimmten Werten einer bestimmten Spalte mit großer Dimension (z. B. einer "Anwendungs-ID" oder einer "Mandanten-ID")
* Es ist unwahrscheinlich, dass die in die Tabelle aufgenommenen Daten gemäß dieser Spalte vorbestellt werden.

Obwohl für die Anzahl der Spalten (Sortierschlüssel), die als Teil der Richtlinie definiert werden können, keine hartcodierten Grenzwerte festgelegt sind, fügt jede zusätzliche Spalte dem Aufnahmeprozess einen gewissen Overhead hinzu, und wenn weitere Spalten hinzugefügt werden, verringert sich die effektive Rückgabe.

> [!NOTE]
> Sobald die Richtlinie auf eine Tabelle angewendet wurde, wirkt sie sich auf datenabhängige Daten aus, die ab diesem Zeitpunkt aufgenommen werden.

Steuerbefehle für die Verwaltung von Zeilenreihenfolgerichtlinien finden Sie [hier](../management/roworder-policy.md)