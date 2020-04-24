---
title: Übersicht über die Verwaltung gespeicherter Funktionen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Übersicht über die Verwaltung gespeicherter Funktionen in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f3768c6252a96215d37bd9f19a44cbf4d3afc731
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520960"
---
# <a name="stored-functions-management-overview"></a>Übersicht über die Verwaltung gespeicherter Funktionen
In diesem Abschnitt werden Steuerbefehle beschrieben, die zum Erstellen und Ändern der folgenden [benutzerdefinierten Funktionen](../query/functions/user-defined-functions.md)verwendet werden:

|Funktion |BESCHREIBUNG|
|---------|-----------|
|[.alter-Funktion](alter-function.md) |Ändert eine vorhandene Funktion und speichert sie in den Datenbankmetadaten |
|[.alter-Funktion docstring](alter-docstring-function.md) |Ändert den DocString-Wert einer vorhandenen Funktion |
|[.alter-Funktionsordner](alter-folder-function.md) |Ändert den Ordnerwert einer vorhandenen Funktion |
|[.create-Funktion](create-function.md) |Erstellt eine gespeicherte Funktion |
|[.create-or-alter-Funktion](create-alter-function.md) |Erstellt eine gespeicherte Funktion oder ändert eine vorhandene Funktion und speichert sie in den Datenbankmetadaten |
|[.drop-Funktion und .drop-Funktionen](drop-function.md) |Löscht eine Funktion (oder Funktionen) aus der Datenbank |
|[.show-Funktionen und .show-Funktion](show-function.md) |Listet alle gespeicherten Funktionen oder eine bestimmte Funktion in der aktuell ausgewählten Datenbank auf |