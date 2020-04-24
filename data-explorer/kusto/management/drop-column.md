---
title: Dropspalte - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Ablagespalte in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 56ba5499b27b517b3080ee27ac317aa1e0917edd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521215"
---
# <a name="drop-column"></a>Drop-Spalte

Entfernt eine Spalte aus einer Tabelle.
Um mehrere Spalten aus einer Tabelle zu löschen, siehe [unten](#drop-table-columns).

> [!WARNING]
> Dieser Befehl ist irreversibel. Alle Daten in der entfernten Spalte werden gelöscht.
> Zukünftige Befehle zum Hinzufügen dieser Spalte zurück können die Daten nicht wiederherstellen.

**Syntax**

`.drop``column` *TableName* `.` *ColumnName*

## <a name="drop-table-columns"></a>Droptabellenspalten

Entfernt eine Reihe von Spalten aus einer Tabelle.

> [!WARNING]
> Dieser Befehl ist irreversibel. Alle Daten in den entfernten Spalten werden gelöscht.
> Zukünftige Befehle zum Hinzufügen dieser Spalten können die Daten nicht wiederherstellen.

**Syntax**

`.drop``table` *Tabellenname* `columns` *Col1* `,` *Col2*Col1 [ Col2 ]... `(``)`