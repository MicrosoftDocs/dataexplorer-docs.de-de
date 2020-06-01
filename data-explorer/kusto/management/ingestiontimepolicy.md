---
title: 'Ingestiontime-Richtlinie: Azure Daten-Explorer'
description: Dieser Artikel beschreibt die ingestiontime-Richtlinie in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 50e0083b1cdbed06106507fe69fb0d039c923c43
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257806"
---
# <a name="ingestiontime-policy"></a>IngestionTime-Richtlinie

Die ingestiontime-Richtlinie ist eine optionale Richtlinie, die für Tabellen (aktiviert) festgelegt werden kann.

Wenn diese Option aktiviert ist, fügt Kusto `datetime` der Tabelle eine verborgene Spalte mit dem Namen hinzu `$IngestionTime` . Wenn nun neue Daten erfasst werden, wird die Erfassungs Zeit in der ausgeblendeten Spalte aufgezeichnet. Diese Zeit wird vom Kusto-Cluster direkt vor dem Commit der Daten gemessen. 

> [!NOTE]
> Jeder Datensatz hat einen eigenen `$IngestionTime` Wert.

Da die Spalte für die Erfassungs Zeit ausgeblendet ist, können Sie Ihren Wert nicht direkt abfragen.
Stattdessen ruft eine spezielle Funktion namens [ingestion_time ()](../query/ingestiontimefunction.md) diesen Wert ab. Wenn `datetime` in der Tabelle keine Spalte vorhanden ist oder die ingestiontime-Richtlinie beim Erfassen eines Datensatzes nicht aktiviert war, wird ein NULL-Wert zurückgegeben.

Die ingestiontime-Richtlinie ist für zwei Haupt Szenarien konzipiert:
* , Damit Benutzer die Latenz beim Erfassen von Daten schätzen können.
  Viele Tabellen mit Protokolldaten verfügen über eine Zeitstempel-Spalte. Der Zeitstempel-Wert wird von der Quelle ausgefüllt und gibt den Zeitpunkt an, zu dem der Datensatz erstellt wurde. Wenn Sie den Wert dieser Spalte mit der Spalte Erfassungs Zeit vergleichen, können Sie die Latenzzeit schätzen, in der die Daten erhalten werden. 
  
  > [!NOTE]
  > Der berechnete Wert ist nur eine Schätzung, weil die Uhren Quelle und Kusto nicht notwendigerweise synchronisiert sind.
  
* Zur Unterstützung von Daten [Bank Cursorn](../management/databasecursor.md) , mit denen Benutzer aufeinander folgende Abfragen ausgeben können, ist die Abfrage auf die Daten beschränkt, die seit der vorherigen Abfrage erfasst wurden.

Weitere Informationen. Weitere Informationen finden Sie in den [Steuerungs Befehlen zum Verwalten der ingestiontime-Richtlinie](../management/ingestiontime-policy.md).
