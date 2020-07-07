---
title: Drop Column-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Drop Column-Spalte in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 770f787493828e897600485c282f3ccb12bb74c4
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966872"
---
# <a name="drop-column"></a>.drop column

Entfernt eine Spalte aus einer Tabelle.
Informationen zum Löschen mehrerer Spalten aus einer Tabelle finden Sie [unten](#drop-table-columns).

> [!WARNING]
> Dieser Befehl ist nicht rückgängig. Alle Daten in der Spalte, die entfernt werden, werden gelöscht.
> Zukünftige Befehle zum Hinzufügen dieser Spalte sind nicht in der Lage, die Daten wiederherzustellen.

**Syntax**

`.drop``column` *TableName* `.` *ColumnName*

## <a name="drop-table-columns"></a>Tabellen Spalten löschen

Entfernt eine Reihe von Spalten aus einer Tabelle.

> [!WARNING]
> Dieser Befehl ist nicht rückgängig. Alle Daten in den Spalten, die entfernt werden, werden gelöscht.
> Zukünftige Befehle zum Hinzufügen dieser Spalten sind nicht in der Lage, die Daten wiederherzustellen.

**Syntax**

`.drop``table` *TableName* `columns` `(` *Col1* [ `,` *Col2*]...`)`