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
ms.openlocfilehash: 569554379ffe672ed75fd15da127f03fea35f6d1
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748381"
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

## <a name="paging-of-query-results"></a>Paging von Abfrage Ergebnissen

Zu den Methoden für die Implementierung von Paging gehören:

* Exportieren Sie das Ergebnis einer Abfrage in einen externen Speicher, und Paging Sie die generierten Daten.
* Schreiben Sie eine Anwendung der mittleren Ebene, die eine Zustands behaftete Paging-API bereitstellt, indem Sie die Ergebnisse einer Kusto-Abfrage zwischenspeichern.
* Verwenden Sie die Paginierung in [gespeicherten Abfrage Ergebnissen](../management/stored-query-results.md#pagination) .


## <a name="see-also"></a>Weitere Informationen

* [sort-Operator](sortoperator.md)
* [top-Operator](topoperator.md)
* [top-nested operator](topnestedoperator.md)
