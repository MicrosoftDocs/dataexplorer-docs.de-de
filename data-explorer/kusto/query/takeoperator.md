---
title: 'Take Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Übernahme des Operators in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 98586f8f8380d4c1fc36a88b288b47798c10e09e
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517919"
---
# <a name="take-operator"></a>take-Operator

Gibt die angegebene Anzahl von Zeilen zurück.

```kusto
T | take 5
```

Es gibt keine Garantie, welche Datensätze zurückgegeben werden, es sei denn, die Quelldaten sind sortiert.

**Syntax**

`take`*Nummeriofrows* 
 `limit` *Nummeriofrows*

( `take` und `limit` sind Synonyme.)

**Notizen**

`take`ist eine einfache, schnelle und effiziente Möglichkeit, eine kleine Stichprobe von Datensätzen anzuzeigen, wenn Daten interaktiv durchsucht werden, aber beachten Sie, dass es keine Konsistenz der Ergebnisse gewährleistet, wenn es mehrmals ausgeführt wird, selbst wenn das DataSet nicht geändert wurde.

Auch wenn die Anzahl der Zeilen, die von der Abfrage zurückgegeben werden, nicht explizit durch die Abfrage begrenzt ist (kein `take` Operator verwendet), schränkt Kusto diese Anzahl standardmäßig ein.
Weitere Informationen finden Sie unter [Kusto-Abfrage Grenzwerte](../concepts/querylimits.md) .

Siehe: [Sort](sortoperator.md) 
 -Operator[Top](topoperator.md) 
 [-Operator Top-netsted Operator](topnestedoperator.md)

## <a name="does-kusto-support-paging-of-query-results"></a>Unterstützt Kusto das Paging von Abfrage Ergebnissen?

Kusto bietet keinen integrierten Paging-Mechanismus.

Kusto ist ein komplexer Dienst, der kontinuierlich die Daten optimiert, die er speichert, um eine hervorragende Abfrageleistung für große Datasets bereitzustellen. Paging ist zwar ein nützlicher Mechanismus für Zustands lose Clients mit eingeschränkten Ressourcen, die Belastung wird jedoch auf den Back-End-Dienst verlagert, der Client Zustandsinformationen nachverfolgen muss. Anschließend ist die Leistung und Skalierbarkeit des Back-End-Dienstanbieter stark eingeschränkt.

Implementieren Sie für Paging-Unterstützung eine der folgenden Funktionen:

* Exportieren des Ergebnisses einer Abfrage in einen externen Speicher und Paging durch die generierten Daten.

* Schreiben einer Anwendung der mittleren Ebene, die eine Zustands behaftete Paging-API bereitstellt, indem die Ergebnisse einer Kusto-Abfrage zwischengespeichert werden.
