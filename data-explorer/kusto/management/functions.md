---
title: 'Übersicht über die Verwaltung gespeicherter Funktionen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Übersicht über die Verwaltung gespeicherter Funktionen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 4354538b206ee86e34941718bcf6d74130647fb6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321351"
---
# <a name="stored-functions-management-overview"></a>Übersicht über die Verwaltung gespeicherter Funktionen
In diesem Abschnitt werden Steuerungsbefehle beschrieben, die zum Erstellen und Ändern der folgenden [benutzerdefinierten Funktionen](../query/functions/user-defined-functions.md)verwendet werden:

|Funktion |BESCHREIBUNG|
|---------|-----------|
|[`.alter function`](alter-function.md) |Ändert eine vorhandene Funktion und speichert Sie in den Daten Bank Metadaten. |
|[`.alter function docstring`](alter-docstring-function.md) |Ändert den DocString-Wert einer vorhandenen Funktion. |
|[`.alter function folder`](alter-folder-function.md) |Ändert den Ordner Wert einer vorhandenen Funktion. |
|[`.create function`](create-function.md) |Erstellt eine gespeicherte Funktion. |
|[`.create-or-alter function`](create-alter-function.md) |Erstellt eine gespeicherte Funktion oder ändert eine vorhandene Funktion und speichert Sie in den Daten Bank Metadaten. |
|[`.drop function` immer `.drop functions`](drop-function.md) |Löscht eine Funktion (oder Funktionen) aus der Datenbank. |
|[`.show functions` immer `.show function`](show-function.md) |Listet alle gespeicherten Funktionen oder eine bestimmte Funktion in der aktuell ausgewählten Datenbank auf. |