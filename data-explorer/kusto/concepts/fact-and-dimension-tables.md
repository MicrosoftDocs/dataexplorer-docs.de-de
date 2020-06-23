---
title: 'Fakten-und Dimensions Tabellen: Azure Daten-Explorer'
description: In diesem Artikel werden Fakten-und Dimensions Tabellen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 6db37366ddd3d70aaa89c0d6eebd1ec8affbb76d
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264441"
---
# <a name="fact-and-dimension-tables"></a>Fakten- und Dimensionstabellen

Wenn Sie das Schema für eine Azure Daten-Explorer-Datenbank entwerfen, stellen Sie sich die Tabellen als eine der beiden Kategorien im großen und ganzen vor.
* [Fakten Tabellen](https://en.wikipedia.org/wiki/Fact_table)
* [Dimensions Tabellen](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)

## <a name="fact-tables"></a>Fakten Tabellen
Fakten Tabellen sind Tabellen, deren Datensätze unveränderlich "Fakten" sind, wie z. b. Dienst Protokolle und Mess Informationen. Datensätze werden in einem Streamingmodus oder in großen Blöcken progressiv an die Tabelle angehängt. Die Datensätze bleiben bestehen, bis Sie aufgrund von Kosten entfernt werden oder weil Sie Ihren Wert verloren haben. Datensätze werden andernfalls nie aktualisiert.

Entitäts Daten werden manchmal in Fakten Tabellen gespeichert, bei denen sich die Entitäts Daten langsam ändern. Beispielsweise Daten zu einer physischen Entität, z. b. eine Office-Ausrüstung, bei der sich selten der Standort ändert.
Da die Daten in Kusto unveränderlich sind, besteht die übliche Vorgehensweise darin, dass jede Tabelle zwei Spalten enthält:
* Eine Identity ( `string` )-Spalte, die die Entität identifiziert.
* Eine Zeitstempel Spalte der letzten Änderung ( `datetime` )

Dann wird nur der letzte Datensatz für jede Entitäts Identität abgerufen.

## <a name="dimension-tables"></a>Dimensions Tabellen
Dimensions Tabellen:
* Halten Sie Verweis Daten, z. b. Nachschlage Tabellen, von einem Entitäts Bezeichner zu ihren Eigenschaften.
* Speichern von Momentaufnahme ähnlichen Daten in Tabellen, deren ganzer Inhalt in einer einzelnen Transaktion geändert wird

Dimensions Tabellen werden nicht regelmäßig mit neuen Daten erfasst. Stattdessen wird der gesamte Dateninhalt mithilfe von Vorgängen wie z [. b.. Set-or-Replace](../management/data-ingestion/ingest-from-query.md), [. Move-Blöcke](../management/extents-commands.md#move-extents)oder [Umbenennungs Tabellen](../management/rename-table-command.md)gleichzeitig aktualisiert.

Manchmal können Dimensions Tabellen aus Fakten Tabellen abgeleitet werden. Dieser Vorgang kann über eine [Aktualisierungs Richtlinie](../management/updatepolicy.md) für die Fakten Tabelle durchgeführt werden, wobei eine Abfrage für die Tabelle durchgeführt wird, die den letzten Datensatz für jede Entität annimmt.

## <a name="differentiate-fact-and-dimension-tables"></a>Fakten-und Dimensions Tabellen unterscheiden

Es gibt Prozesse in Kusto, die zwischen Fakten Tabellen und Dimensions Tabellen unterscheiden. Eine davon ist der [fortlaufende Export](../management/data-export/continuous-data-export.md).

Diese Mechanismen werden sichergestellt, dass Daten in Fakten Tabellen genau einmal verarbeitet werden. Sie verlassen sich auf den [Daten Bank Cursor](../management/databasecursor.md) Mechanismus.

Beispielsweise werden bei jeder Ausführung eines fortlaufenden Export Auftrags alle Datensätze exportiert, die seit dem letzten Update des Daten Bank Cursors erfasst wurden. Fortlaufende Exportaufträge müssen zwischen Fakten Tabellen und Dimensions Tabellen unterscheiden. In Fakten Tabellen werden nur neu erfasste Daten verarbeitet, und Dimensions Tabellen werden als Suchvorgänge verwendet. Daher muss die gesamte Tabelle berücksichtigt werden.

Es gibt keine Möglichkeit, eine Tabelle als "Fakten Tabelle" oder "Dimensions Tabelle" zu markieren.
Die Art und Weise, wie Daten in die Tabelle aufgenommen werden und wie die Tabelle verwendet wird, ist der Typ, der den Typ identifiziert.
