---
title: Das Kusto Daten-Explorer-Abfrageresultset überschreitet das interne Limit
description: In diesem Artikel wird beschrieben, wie das Resultset der Abfrage den internen... Limit in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9706cce08a99989441aa657c5d85465294be837e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512417"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>Das Resultset der Abfrage hat den internen Wert überschritten... ans

Ein *Abfrageresultset hat den internen... "Limit* " ist ein [Teil eines Abfrage Fehlers](partialqueryfailures.md) , der auftritt, wenn das Ergebnis der Abfrage einen von zwei Grenzwerten überschritten hat:
* Ein Grenzwert für die Anzahl der Datensätze ( `record count limit` standardmäßig auf 500.000 festgelegt).
* Ein Grenzwert für die Gesamtmenge der Daten ( `data size limit` , standardmäßig auf 67.108.864 (64 MB) festgelegt).

Es gibt mehrere mögliche Aktions Kurse:

* Ändern Sie die Abfrage so, dass weniger Ressourcen beansprucht werden. 
  Beispielsweise können Sie folgende Aktionen ausführen:
  * Begrenzen der Anzahl der von der Abfrage zurückgegebenen Datensätze mit dem [Take-Operator](../query/takeoperator.md) oder Hinzufügen zusätzlicher [WHERE-Klauseln](../query/whereoperator.md)
  * Versuchen Sie, die Anzahl der von der Abfrage zurückgegebenen Spalten zu verringern. Verwenden des [Project-Operators](../query/projectoperator.md)oder des [Projekt entfernten Operators](../query/projectawayoperator.md))
  * Verwenden Sie den Zusammenfassungs [Operator](../query/summarizeoperator.md) , um aggregierte Daten zu erhalten.
* Erhöhen Sie das relevante Abfragelimit für diese Abfrage vorübergehend. Weitere Informationen finden Sie unter **Abschneiden** von Ergebnissen unter [Abfrage Limits](querylimits.md)).

 > [!NOTE] 
 > Es wird nicht empfohlen, das Abfragelimit zu erhöhen, da die Grenzwerte zum Schutz des Clusters vorhanden sind. Durch die Grenzwerte wird sichergestellt, dass bei einer einzelnen Abfrage nicht gleichzeitige Abfragen im Cluster unterbrochen werden.
  
