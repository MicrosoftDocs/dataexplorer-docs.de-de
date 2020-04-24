---
title: Spalte umbenennen - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Umbenennungsspalte in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 02e692e01bb8eb0abd49c9673b000b722734db90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520501"
---
# <a name="rename-column"></a>Umbenennungsspalte

Ändert den Namen einer vorhandenen Tabellenspalte.
Um den Namen mehrerer Spalten zu ändern, siehe [unten](#rename-columns).

**Syntax**

`.rename``column` [*DatabaseName* `.`] *TableName* `.` *ColumnExistingName* `to` *ColumnNewName*

Wobei *DatabaseName*, *TableName*, *ColumnExistingName*und *ColumnNewName* die Namen der jeweiligen Entitäten sind und den [Idbezeichnerbenennungsregeln](../query/schema-entities/entity-names.md)folgen .

## <a name="rename-columns"></a>Umbenennen von Spalten

Ändert die Namen mehrerer vorhandener Spalten in derselben Tabelle.

**Syntax**

`.rename``columns` *Col1* `=` [*Datenbankname* `.` [*Tabellenname* `.` *Col2*]] `,` ]] ...

Der Befehl kann verwendet werden, um die Namen von zwei Spalten zu tauschen (jede wird in den Namen der anderen umbenannt).