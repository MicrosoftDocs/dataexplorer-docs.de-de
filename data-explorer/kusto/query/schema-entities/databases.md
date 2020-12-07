---
title: 'Datenbanken: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel werden Datenbanken in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 3d981a4b56eef689081a4bc6a622221c126efa2e
ms.sourcegitcommit: 8b8228fe18e6408673891374a8048a7a3723921c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96516063"
---
# <a name="databases"></a>Datenbanken

Kusto folgt einem Beziehungsmodell, in dem die Daten gespeichert werden, bei denen die Entität der oberen Ebene eine ist `database` . 

Der Kusto-Cluster kann mehrere Datenbanken hosten, wobei jede Datenbank eine eigene Sammlung von [Tabellen](tables.md), [gespeicherten Funktionen](stored-functions.md)und [externen Tabellen](externaltables.md)hostet.
Jede Datenbank verfügt über eigene Berechtigungen basierend auf dem [rollenbasierten Access Control Modell (RBAC)](../../management/access-control/index.md) .

**Hinweise**  

* Datenbanknamen müssen den Regeln für [Entitäts Namen](./entity-names.md)entsprechen.
* Der maximale Grenzwert für Datenbanken pro Cluster beträgt 10.000.
