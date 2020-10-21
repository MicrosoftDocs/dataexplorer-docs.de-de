---
title: 'Take Operator: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Übernahme des Operators in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5192bb2d752a5754ae36840611b9f7b0e84da256
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250718"
---
# <a name="take-operator"></a>take-Operator

Gibt die angegebene Anzahl von Zeilen zurück.

```kusto
T | take 5
```

Es gibt keine Garantie, welche Datensätze zurückgegeben werden, es sei denn, die Quelldaten sind sortiert.

> [!NOTE]
> `take` ist eine einfache, schnelle und effiziente Möglichkeit, eine kleine Stichprobe von Datensätzen anzuzeigen, wenn Daten interaktiv durchsucht werden, aber beachten Sie, dass es keine Konsistenz der Ergebnisse gewährleistet, wenn es mehrmals ausgeführt wird, selbst wenn das DataSet nicht geändert wurde.
> Auch wenn die Anzahl der Zeilen, die von der Abfrage zurückgegeben werden, nicht explizit durch die Abfrage begrenzt ist (kein `take` Operator verwendet), schränkt Kusto diese Anzahl standardmäßig ein. Weitere Informationen finden Sie unter [Kusto-Abfrage Limits](../concepts/querylimits.md).

## <a name="syntax"></a>Syntax

`take`*Nummeriofrows* 
 `limit` *Nummeriofrows*

( `take` und `limit` sind Synonyme.)

## <a name="does-kusto-support-paging-of-query-results"></a>Unterstützt Kusto das Paging von Abfrage Ergebnissen?

Kusto bietet keinen integrierten Paging-Mechanismus.

Kusto ist ein komplexer Dienst, der kontinuierlich die Daten optimiert, die er speichert, um eine hervorragende Abfrageleistung für große Datasets bereitzustellen. Paging ist zwar ein nützlicher Mechanismus für Zustands lose Clients mit eingeschränkten Ressourcen, die Belastung wird jedoch auf den Back-End-Dienst verlagert, der Client Zustandsinformationen nachverfolgen muss. Anschließend ist die Leistung und Skalierbarkeit des Back-End-Dienstanbieter stark eingeschränkt.

Implementieren Sie für Paging-Unterstützung eine der folgenden Funktionen:

* Exportieren des Ergebnisses einer Abfrage in einen externen Speicher und Paging durch die generierten Daten.

* Schreiben einer Anwendung der mittleren Ebene, die eine Zustands behaftete Paging-API bereitstellt, indem die Ergebnisse einer Kusto-Abfrage zwischengespeichert werden.

## <a name="see-also"></a>Weitere Informationen

* [sort-Operator](sortoperator.md)
* [top-Operator](topoperator.md)
* [top-nested operator](topnestedoperator.md)