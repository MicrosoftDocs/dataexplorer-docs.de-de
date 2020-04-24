---
title: Entitätsverweise - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Entitätsverweise in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abbf63de632ff2ff5fb721dddc256c5ee62d4966
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509400"
---
# <a name="entity-references"></a>Entitätsverweise

Benannte Kusto-Schemaentitäten (Datenbanken, Tabellen, Spalten und gespeicherte Funktionen, jedoch keine Cluster) werden in der Abfrage mithilfe ihrer Namen referenziert. Wenn der Container der Entität im aktuellen Kontext eindeutig ist, kann der Entitätsname ohne zusätzliche Qualifikationen verwendet werden. Wenn Sie z. B. eine `DB`Abfrage für eine `T` Datenbank mit dem Namen `T`ausführen, kann man auf eine Tabelle verweisen, die in dieser Datenbank aufgerufen wird, indem Sie einfach ihren Namen verwenden.

Wenn der Container der Entität hingegen nicht aus dem Kontext verfügbar ist oder man eine Entität aus einem anderen Container als dem Container im Kontext referenzieren möchte, muss man den **qualifizierten Namen**der Entität verwenden, d. h. die Verkettung des Entitätsnamens mit dem Container (und möglicherweise dem des Containers usw.). Daher kann eine Abfrage, die `DB` für `T1` die Datenbank `DB1` ausgeführt wird, `database("DB1").T1`mithilfe von auf eine Tabelle in einer anderen Datenbank desselben Clusters `cluster("https://C2.kusto.windows.net/").database("DB2").T2`verweisen, und wenn sie auf eine Tabelle aus einem anderen Cluster verweisen möchte, kann sie dies z. B. mithilfe von tun.

Entitätsverweise können auch den hübschen Namen der Entität sein, solange sie im Kontext des Entitätscontainers eindeutig ist. Siehe [Entität hübsche Namen](./entity-names.md#entity-pretty-names).

## <a name="wildcard-matching-for-entity-names"></a>Platzhalterabgleich für Entitätsnamen

In einigen Kontexten kann man`*`einen Platzhalter ( ) verwenden, um einen Entitätsnamen ganz oder teilweise abzugleichen. Die folgende Abfrage verweist z. B. auf alle Tabellen in `DB` der aktuellen `T`Datenbank sowie auf alle Tabellen in der Datenbank, deren Name mit einem beginnt:

```kusto
union *, database("DB1").T*
```

Hinweis: Der Platzhalterabgleich stimmt nicht mit`$`Entitätsnamen überein, die mit einem Dollarzeichen beginnen ( ).
Solche Namen sind systemreserviert.



