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
ms.openlocfilehash: 41a9851405ff85210bba7728a3737fc3b0a57f70
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382385"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>Das Resultset der Abfrage hat den internen Wert überschritten... ans

Ein *Abfrageresultset hat den internen... "Limit* " ist ein [Teil eines Abfrage Fehlers](partialqueryfailures.md) , der auftritt, wenn das Ergebnis der Abfrage einen von zwei Grenzwerten überschritten hat:
* Ein Grenzwert für die Anzahl der Datensätze ( `record count limit` standardmäßig auf 500.000 festgelegt).
* Ein Grenzwert für die Gesamtmenge der Daten ( `data size limit` , standardmäßig auf 67.108.864 (64 MB) festgelegt). 

Es gibt mehrere mögliche Vorgehensweisen, wenn dies geschieht:
* Ändern Sie die Abfrage so, dass weniger Ressourcen beansprucht werden. Beispielsweise können Sie versuchen, die Anzahl der von der Abfrage zurückgegebenen Datensätze (mit dem [Take-Operator](../query/takeoperator.md) oder durch Hinzufügen zusätzlicher [WHERE-Klauseln](../query/whereoperator.md)) zu begrenzen oder die Anzahl der von der Abfrage zurückgegebenen Spalten (entweder mithilfe des [Projekt Operators](../query/projectoperator.md) oder des [Projekt entfernten Operators](../query/projectawayoperator.md)) zu begrenzen oder den Zusammenfassungs [Operator](../query/summarizeoperator.md) zu verwenden, um aggregierte Daten usw. zu erhalten.
* Erhöhen Sie das relevante Abfragelimit für diese Abfrage temporär (Weitere Informationen finden Sie unter **Abschneiden** von Ergebnissen unter [Abfrage Limits](querylimits.md)).  
  Beachten Sie, dass dies im Allgemeinen nicht empfohlen wird, da die Grenzwerte für den Schutz des Clusters spezifisch sind, um sicherzustellen, dass eine einzelne Abfrage im Cluster ausgeführte Abfragen nicht unterbricht.