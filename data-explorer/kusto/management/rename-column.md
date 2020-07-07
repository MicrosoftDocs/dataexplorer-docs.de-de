---
title: 'Spalte umbenennen: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Spalte umbenennen in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25d0ff106d406c59c24d26542a8dad1e4992e311
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967382"
---
# <a name="rename-column"></a>Umbenennen der Spalte

Ändert den Namen einer vorhandenen Tabellenspalte.
Informationen zum Ändern des Namens mehrerer Spalten finden Sie [unten](#rename-columns).

**Syntax**

`.rename``column`[*DatabaseName* `.` ] *TableName* `.` *columnetxistingname* `to` *columnnewname*

Dabei sind *DatabaseName*, *TableName*, *columnxistingname*und *columnnewname* die Namen der jeweiligen Entitäten, und befolgen Sie die [Benennungs Regeln für den Bezeichner](../query/schema-entities/entity-names.md).

## <a name="rename-columns"></a>Umbenennen von Spalten

Ändert die Namen mehrerer vorhandener Spalten in derselben Tabelle.

**Syntax**

`.rename``columns` *Col1* `=` [*DatabaseName* `.` [*TableName* `.` *Col2*]] `,` ...

Der Befehl kann verwendet werden, um die Namen von zwei Spalten auszutauschen (jeder wird in den Namen des anderen umbenannt).