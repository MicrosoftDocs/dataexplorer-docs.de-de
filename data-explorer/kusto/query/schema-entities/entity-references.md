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
ms.openlocfilehash: fc36f31bcdb90ed270a4ad21874d121d91fe429e
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342687"
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

* [Schema Entitäts Typen](./index.md)
* [Schema Entitäts Namen](./entity-names.md)