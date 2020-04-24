---
title: take-Operator - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird der Take-Operator in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0c8f724e139e13bf9ece00d5af09f2a3cf25b03a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506595"
---
# <a name="take-operator"></a>take-Operator

Zurück zur angegebenen Anzahl von Zeilen.

```kusto
T | take 5
```

Es gibt keine Garantie, welche Datensätze zurückgegeben werden, es sei denn, die Quelldaten werden sortiert.

**Syntax**

`take`*NumberOfRows* 
 `limit` *NumberOfRows*

(`take` `limit` und sind Synonyme.)

**Hinweise**

`take`ist eine einfache, schnelle und effiziente Möglichkeit, eine kleine Stichprobe von Datensätzen anzuzeigen, wenn Sie Daten interaktiv durchsuchen, aber beachten Sie, dass sie keine Konsistenz in ihren Ergebnissen garantiert, wenn sie mehrmals ausgeführt wird, auch wenn sich der Datensatz nicht geändert hat.

Auch wenn die Anzahl der von der Abfrage zurückgegebenen `take` Zeilen nicht explizit durch die Abfrage eingeschränkt wird (kein Operator wird verwendet), schränkt Kusto diese Zahl standardmäßig ein.
Weitere Informationen finden Sie unter [Kusto-Abfragelimits.](../concepts/querylimits.md)

Siehe: [Sortieroperator](sortoperator.md)
[Topoperator](topoperator.md)
[top-nested Operator](topnestedoperator.md)

## <a name="does-kusto-support-paging-of-query-results"></a>Unterstützt Kusto das Paging von Abfrageergebnissen?

Kusto bietet keinen integrierten Paging-Mechanismus.

Kusto ist ein komplexer Service, der die gespeicherten Daten kontinuierlich optimiert, um eine hervorragende Abfrageleistung über riesige Datensätze zu bieten. Während Paging ein nützlicher Mechanismus für zustandslose Clients mit begrenzten Ressourcen ist, verlagert es die Last auf den Back-End-Dienst, der Clientstatusinformationen nachverfolgen muss. In der Folge sind die Leistung und Skalierbarkeit des Back-End-Dienstes stark eingeschränkt.

Implementieren Sie für die Pagingunterstützung eines der folgenden Features:

* Exportieren des Ergebnisses einer Abfrage in einen externen Speicher und Paging durch die generierten Daten.

* Schreiben einer Anwendung der mittleren Ebene, die eine zustandsbehaftete Paging-API bereitstellt, indem die Ergebnisse einer Kusto-Abfrage zwischenspeichern werden.