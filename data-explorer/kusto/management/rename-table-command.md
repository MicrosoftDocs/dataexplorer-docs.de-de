---
title: . RENAME TABLE und. rename Tables-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Tabellen und Umbenennungs Tabellen in Azure Daten-Explorer umbenennen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a1644021d1e2df294782d3b24e111d3c57af5f91
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617577"
---
# <a name="rename-table-and-rename-tables"></a>Umbenennen von Tabellen und Umbenennen von Tabellen

Ändert den Namen einer vorhandenen Tabelle.

Der `.rename tables` -Befehl ändert den Namen einer Anzahl von Tabellen in der Datenbank als einzelne Transaktion.

Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md)für die Datenbank.

**Syntax**

`.rename``table` *OldName* *NewName* OldName `to` newname

`.rename``tables` *NewName* = *OldName* [`ifexists`] [`,` ...]

> [!NOTE]
> * *OldName* ist der Name einer vorhandenen Tabelle. Ein Fehler wird ausgelöst, und der gesamte Befehl schlägt fehl (hat keine Auswirkung), wenn *OldName* eine vorhandene Tabelle nicht benennt `ifexists` , es sei denn, wird angegeben (in diesem Fall wird dieser Teil des rename-Befehls ignoriert).
> * *NewName* ist der neue Name der vorhandenen Tabelle, die als *OldName*verwendet wurde.
> * Wenn `ifexists` angegeben wird, wird das Verhalten des-Befehls dahingehend geändert, dass das Umbenennen von Teilen nicht vorhandener Tabellen ignoriert wird.

**Anmerkungen**

Mit diesem Befehl werden Tabellen der Datenbank nur im Gültigkeitsbereich angewendet.
Tabellennamen können nicht mit Cluster-oder Datenbanknamen qualifiziert werden.

Mit diesem Befehl werden weder neue Tabellen erstellt noch vorhandene Tabellen entfernt.
Die Transformation, die durch den-Befehl beschrieben wird, muss so lauten, dass sich die Anzahl der Tabellen in der Datenbank nicht ändert.

Der Befehl **unter** stützt das Austauschen von Tabellennamen oder komplexeren Permutationen, sofern Sie den oben aufgeführten Regeln entsprechen. Erfassen Sie z. b. Daten in mehreren Stagingtabellen, und tauschen Sie diese dann mit vorhandenen Tabellen in einer einzigen Transaktion aus.

**Beispiele**

Stellen Sie sich eine Datenbank mit den folgenden `A`Tabellen `B`vor `C`:, `A_TEMP`, und.
Der folgende Befehl `A` tauscht und `A_TEMP` aus (damit die `A_TEMP` Tabelle jetzt aufgerufen `A`wird, und umgekehrt), benennen `B` Sie in um und behalten `NEWB` `C` Sie unverändert bei. 

```kusto
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

Die folgende Sequenz von Befehlen:
1. Erstellt eine neue temporäre Tabelle.
1. Ersetzt eine vorhandene oder nicht vorhandene Tabelle durch die neue Tabelle.

```kusto
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```
