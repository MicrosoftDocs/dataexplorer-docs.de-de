---
title: IngestionTime-Richtlinie - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die IngestionTime-Richtlinie in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 0c1755115a9e9f4c60c7f5574eb9b784520ecb88
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520824"
---
# <a name="ingestiontime-policy"></a>IngestionTime-Richtlinie

Die IngestionTime-Richtlinie ist eine optionale Richtlinie, die für Tabellen festgelegt (aktiviert) werden kann.

Wenn diese Richtlinie für eine Tabelle aktiviert `datetime` ist, fügt Kusto der Tabelle eine ausgeblendete Spalte mit dem Namen hinzu. `$IngestionTime` Ab diesem Zeitpunkt wird bei jeder Aufnahme neuer Daten in die Tabelle der Zeitpunkt der Aufnahme (gemessen vom Kusto-Cluster kurz vor dem Festizieren der Daten) in der ausgeblendeten Spalte für alle aufgenommenen Datensätze aufgezeichnet. (Beachten Sie, dass `$IngestionTime` jeder Datensatz seinen eigenen Wert hat, genau wie jede andere Spalte.)

Da die Aufnahmezeitspalte ausgeblendet ist, kann man ihren Wert nicht direkt abfragen.
Stattdessen wird eine spezielle Funktion namens [ingestion_time()](../query/ingestiontimefunction.md) bereitgestellt, um diesen Wert abzurufen. Wenn keine solche Spalte in der Tabelle vorhanden ist oder die IngestionTime-Richtlinie nicht aktiviert wurde, als ein Datensatz aufgenommen wurde, wird ein NULL-Wert zurückgegeben.

Die IngestionTime-Richtlinie wurde für zwei Hauptszenarien entwickelt:
* Damit Benutzer die End-to-End-Latenz beim Einleiten von Daten abschätzen können.
  Viele Tabellen, die Protokolldaten enthalten, haben eine Zeitstempelspalte, deren Wert von der Quelle ausgefüllt wird, um den Zeitpunkt anzugeben, zu dem der Datensatz erstellt wurde. Durch den Vergleich des Wertes dieser Spalte mit der Spalte Aufnahmezeit kann die Latenz beim Abrufen der Daten geschätzt werden. (Beachten Sie, dass dies nur eine Schätzung ist, da die Quelle und Kusto nicht unbedingt ihre Uhren synchronisiert haben.)
* Zur Unterstützung von [Datenbankcursors](../management/databasecursor.md)können Benutzer aufeinander folgende Abfragen ausstellen und die Abfrage jedes Mal auf Daten beschränken, die seit der vorherigen Abfrage aufgenommen wurden.



Weitere Informationen zu den Steuerbefehlen zum Verwalten der IngestionTime-Richtlinie [finden Sie hier](../management/ingestiontime-policy.md).