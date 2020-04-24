---
title: Datenbanken - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden Datenbanken in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a94b168d8aa8443251f3a01dc659e114b0aacaf5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509434"
---
# <a name="databases"></a>Datenbanken

Kusto folgt einem Beziehungsmodell zum Speichern der `database`Daten, bei denen eine Entität der oberen Ebene eine ist. 

Der Kusto-Cluster kann mehrere Datenbanken hosten, in denen jede Datenbank eine eigene Sammlung von [Tabellen,](tables.md) [gespeicherten Funktionen](stored-functions.md)und [externen Tabellen hosten](externaltables.md)wird.
Jede Datenbank verfügt über einen eigenen Berechtigungssatz, der auf dem [RBAC-Modell (Role Based Access Control)](../../management/access-control/index.md) basiert.

**Hinweise**  

* Bei Datenbanknamen wird die Groß-/Kleinschreibung nicht berücksichtigt.
* Datenbanknamen müssen den Regeln für [Entitätsnamen](./entity-names.md)folgen.
* Die maximale Anzahl von Datenbanken pro Cluster beträgt 10.000.