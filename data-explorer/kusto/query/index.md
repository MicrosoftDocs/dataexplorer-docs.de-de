---
title: 'Übersicht: Azure Data Explorer'
description: Dieser Artikel enthält eine Übersicht über Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/07/2019
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: d0f71416410812b8160354781111f4a6f46350fd
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359691"
---
# <a name="overview"></a>Übersicht

Eine Kusto-Abfrage ist eine schreibgeschützte Anforderung zur Verarbeitung von Daten und zur Rückgabe der Ergebnisse.
Die Anforderung wird in unformatiertem Text formuliert, wobei ein Datenflussmodell verwendet wird, das darauf ausgelegt ist, die Lesbarkeit, das Verfassen und die Automatisierung der Syntax zu vereinfachen. Die Abfrage verwendet Schemaentitäten, die in einer SQL-ähnlichen Hierarchie organisiert sind: Datenbanken, Tabellen und Spalten.

Die Abfrage besteht aus einer Abfolge von Abfrageanweisungen, jeweils getrennt durch ein Semikolon (`;`), mit mindestens einer [tabellarischen Ausdrucksanweisung](tabularexpressionstatements.md), bei der es sich um eine Anweisung handelt, die Daten in einem tabellenähnlichen Raster aus Spalten und Zeilen erzeugt. Die tabellarischen Ausdrucksanweisungen der Abfrage erzeugen die Ergebnisse der Abfrage.

Die Syntax der tabellarischen Ausdrucksanweisung besteht aus einem tabellarischen Datenfluss von einem tabellarischen Abfrageoperator zu einem anderen. Dieser beginnt mit einer Datenquelle (z. B. mit einer Tabelle in einer Datenbank oder mit einem Operator, der Daten erzeugt) und durchläuft anschließend eine Reihe von Datentransformationsoperatoren, die mit einem senkrechten Strich (`|`) als Trennzeichen miteinander verbunden sind.

Die folgende Kusto-Abfrage enthält beispielsweise eine einzelne Anweisung, bei der es sich um eine tabellarische Ausdrucksanweisung handelt. Die Anweisung beginnt mit einem Verweis auf eine Tabelle namens `StormEvents` (die Datenbank, die diese Tabelle hostet, ist hier impliziert und Teil der Verbindungsinformationen). Die Daten (Zeilen) für diese Tabelle werden dann zuerst nach dem Wert der Spalte `StartTime` gefiltert und anschließend nach dem Wert der Spalte `State`. Die Abfrage gibt dann die Anzahl der "verbleibenden" Zeilen zurück.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count 
```

Klicken Sie [hier](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAwsuyS/KdS1LzSspVuDlqlEoz0gtSlUILkksKgnJzE1VsLNVSEksSS0BsjWMDAzMdQ0NdQ0MNRUS81KQVNmgKzICKUIxryRVwdZWQcnNxz/I08VRSQFsW3J+aV6JAgAwMx4+hAAAAA==), um diese Abfrage auszuführen.
In diesem Fall sieht das Ergebnis wie folgt aus:

|Anzahl|
|-----|
|   23|
