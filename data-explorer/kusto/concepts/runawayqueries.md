---
title: 'Runaway-Abfragen: Azure Daten-Explorer'
description: Dieser Artikel beschreibt Runaway-Abfragen in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8300289cb98e2f23613c15a711982efe06f327cf
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780251"
---
# <a name="runaway-queries"></a>Endlosabfragen

Bei einer *endlos-Abfrage* handelt es sich um einen [partiellen Abfrage Fehler](partialqueryfailures.md) , der auftritt, wenn ein internes [Abfragelimit](querylimits.md) während der Abfrage Ausführung überschritten wurde. 

Der folgende Fehler kann z. b. gemeldet werden:`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

Es gibt mehrere mögliche Aktions Kurse.
* Ändern Sie die Abfrage so, dass weniger Ressourcen beansprucht werden. Wenn der Fehler beispielsweise anzeigt, dass das Abfrageresultset zu groß ist, können Sie folgende Aktionen durchführen:
  * Begrenzen der Anzahl von Datensätzen, die von der Abfrage zurückgegeben werden
     * Verwenden des [Take-Operators](../query/takeoperator.md)
     * Hinzufügen zusätzlicher [WHERE-Klauseln](../query/whereoperator.md)
  * Reduzieren Sie die Anzahl von Spalten, die von der Abfrage zurückgegeben werden. 
     * Verwenden des [Project-Operators](../query/projectoperator.md)
     * Verwenden des " [Project-Away"-Operators](../query/projectawayoperator.md)
  * Verwenden Sie den zusammen [fassoperator](../query/summarizeoperator.md) , um aggregierte Daten zu erhalten.
* Erhöhen Sie das relevante Abfragelimit für diese Abfrage vorübergehend. Weitere Informationen finden Sie unter [Abfrage Limits-Limit für Arbeitsspeicher pro Iterator](querylimits.md). Diese Methode wird jedoch nicht empfohlen. Die Grenzen liegen vor, um den Cluster zu schützen und um sicherzustellen, dass eine einzelne Abfrage nicht die gleichzeitigen Abfragen unterbricht, die im Cluster ausgeführt werden.
