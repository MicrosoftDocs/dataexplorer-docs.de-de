---
title: Auslaufabfragen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Runaway-Abfragen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 076afea64cf3c4405f37e9e4014584b90d58ca07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522983"
---
# <a name="runaway-queries"></a>Endlosabfragen

Eine *entlaufene Abfrage* ist eine Art [partieller Abfragefehler,](partialqueryfailures.md) der auftritt, wenn während der Abfrageausführung ein interner [Abfragegrenzwert](querylimits.md) überschritten wurde.

Beispielsweise kann der folgende Fehler gemeldet werden:`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

In diesem Fall gibt es mehrere mögliche Vorgehensweisen:
* Ändern Sie die Abfrage so, dass weniger Ressourcen verbraucht werden. Wenn der Fehler beispielsweise angibt, dass das Abfrageergebnissatz zu groß ist, können Sie versuchen, die Anzahl der von der Abfrage zurückgegebenen Datensätze zu begrenzen (mit dem [take-Operator](../query/takeoperator.md) oder durch Hinzufügen zusätzlicher [where-Klauseln](../query/whereoperator.md)), oder die Anzahl der von der Abfrage zurückgegebenen Spalten (entweder mit dem [Projektoperator](../query/projectoperator.md) oder dem [Projekt-Away-Operator)](../query/projectawayoperator.md)zu reduzieren oder den [Parameteroperator](../query/summarizeoperator.md) zum Abrufen aggregierter Daten usw. zu verwenden.
* Erhöhen Sie den relevanten Abfragegrenzwert vorübergehend für diese Abfrage (siehe **Max. Arbeitsspeicher pro Ergebnissatziterator** unter [Abfragelimits](querylimits.md)).  
  Beachten Sie, dass dies im Allgemeinen nicht empfohlen wird, da die Grenzwerte vorhanden sind, um den Cluster speziell zu schützen, um sicherzustellen, dass eine einzelne Abfrage gleichzeitige Abfragen, die auf dem Cluster ausgeführt werden, nicht unterbricht.
  
  
  