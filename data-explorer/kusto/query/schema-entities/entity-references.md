---
title: 'Entitäts Verweise: Azure Daten-Explorer'
description: In diesem Artikel werden Entitäts Verweise in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d652ea8551a21d542ad6afef575616e7387183f
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517885"
---
# <a name="entity-references"></a>Entitätsverweise

Verweisen Sie in einer Abfrage auf Kusto-Schema Entitäten, indem Sie deren Namen verwenden. Gültige Entitäts Namen sind *Datenbanken*, *Tabellen*, *Spalten*und gespeicherte Funktionen. Auf *Cluster* kann nicht mit ihren Namen verwiesen werden.
Wenn der Container der Entität im aktuellen Kontext eindeutig ist, verwenden Sie den Entitäts Namen ohne zusätzliche Qualifikationen. Wenn Sie z. b. eine Abfrage für eine Datenbank `DB` mit dem Namen ausführen, können Sie `T` mit dem Namen auf eine Tabelle namens in dieser Datenbank verweisen `T` .

Wenn der Container der Entität nicht im Kontext verfügbar ist oder Sie auf eine Entität aus einem anderen als dem Container im Kontext verweisen möchten, verwenden Sie den **qualifizierten Namen**der Entität.
Der Name ist die Verkettung des Entitäts namens mit dem des Containers und potenziell dessen Container usw. Auf diese Weise kann eine Abfrage, die für die-Datenbank ausgeführt `DB` wird, auf eine Tabelle `T1` in einer anderen Datenbank `DB1` desselben Clusters verweisen, indem verwendet wird `database("DB1").T1` . Wenn die Abfrage auf eine Tabelle von einem anderen Cluster verweisen soll, kann Sie beispielsweise mithilfe von verwendet werden `cluster("https://C2.kusto.windows.net/").database("DB2").T2` .

Entitäts Verweise können auch die Entität "Pretty Name" verwenden, sofern Sie im Kontext des Containers der Entität eindeutig ist. Weitere Informationen finden Sie unter [Entitäts Namen](./entity-names.md#entity-pretty-names).

## <a name="wildcard-matching-for-entity-names"></a>Platzhalter Vergleiche für Entitäts Namen

In einigen Kontexten können Sie einen Platzhalter ( `*` ) verwenden, um den gesamten oder einen Teil eines Entitäts namens abzugleichen. Die folgende Abfrage verweist z. b. auf alle Tabellen in der aktuellen Datenbank und auf alle Tabellen in der Datenbank, `DB` deren Name mit einem beginnt `T` :

```kusto
union *, database("DB1").T*
```

> [!NOTE]
> Platzhalter Vergleiche können nicht mit Entitäts Namen übereinstimmen, die mit einem Dollarzeichen ( `$` ) beginnen.
Solche Namen sind System reserviert.

## <a name="next-steps"></a>Nächste Schritte

* [Schema Entitäts Typen](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/)
* [Schema Entitäts Namen](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)
