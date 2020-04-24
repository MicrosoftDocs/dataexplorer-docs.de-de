---
title: Abfrageergebnissatz hat die interne ... Limit - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt, dass das Abfrageergebnissatz die interne ... in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb07e68922a88e2cf59ef1eefeabd3af085aed4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523017"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>Abfrageergebnissatz hat die interne ... Limit

Ein *Abfrageergebnissatz hat die interne ... Limit* ist eine Art [partieller Abfragefehler,](partialqueryfailures.md) der auftritt, wenn das Abfrageergebnis einen von zwei Grenzwerten überschritten hat:
* Ein Limit für die`record count limit`Anzahl der Datensätze ( , standardmäßig auf 500.000 festgelegt).
* Ein Limit für die Gesamtdatenmenge (`data size limit`, standardmäßig auf 67.108.864 (64 MB) festgelegt). 

In diesem Fall gibt es mehrere mögliche Vorgehensweisen:
* Ändern Sie die Abfrage so, dass weniger Ressourcen verbraucht werden. Sie können z. B. versuchen, die Anzahl der von der Abfrage zurückgegebenen Datensätze zu begrenzen (mit dem [take-Operator](../query/takeoperator.md) oder durch Hinzufügen zusätzlicher [where-Klauseln](../query/whereoperator.md)), oder die Anzahl der von der Abfrage zurückgegebenen Spalten zu reduzieren (entweder mit dem [Projektoperator](../query/projectoperator.md) oder dem [Projekt-Entfernt-Operator),](../query/projectawayoperator.md)oder den [Summarize-Operator](../query/summarizeoperator.md) verwenden, um aggregierte Daten abzusuchen usw.
* Erhöhen Sie das relevante Abfragelimit für diese Abfrage vorübergehend (siehe **Ergebniskürzung** unter [Abfragelimits](querylimits.md)).  
  Beachten Sie, dass dies im Allgemeinen nicht empfohlen wird, da die Grenzwerte vorhanden sind, um den Cluster speziell zu schützen, um sicherzustellen, dass eine einzelne Abfrage gleichzeitige Abfragen, die auf dem Cluster ausgeführt werden, nicht unterbricht.