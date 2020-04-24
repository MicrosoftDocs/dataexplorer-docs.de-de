---
title: .rename-Tabellen und .rename-Tabellen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden .rename-Tabellen und .rename-Tabellen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: db3e38c562573f52df70979b71fe72d8947158bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520484"
---
# <a name="rename-table-and-rename-tables"></a>.rename Tabellen und .rename Tabellen

Ändert den Namen einer vorhandenen Tabelle.

Der `.rename tables` Befehl ändert den Namen einer Reihe von Tabellen in der Datenbank als eine einzelne Transaktion.

Erfordert [Datenbankadministratorberechtigung](../management/access-control/role-based-authorization.md).

**Syntax**

`.rename``table` *OldName* `to` *NewName*

`.rename``tables` *NewName* = *OldName* [`ifexists`] [`,` ...]

> [!NOTE]
> * *OldName* ist der Name einer vorhandenen Tabelle. Ein Fehler wird ausgelöst, und der gesamte Befehl schlägt fehl (hat keine `ifexists` Auswirkung), wenn *OldName* keine vorhandene Tabelle benennt, es sei denn, es wird angegeben (in diesem Fall wird dieser Teil des Befehls umbenennen ignoriert).
> * *NewName* ist der neue Name der vorhandenen Tabelle, die früher *OldName*hieß.
> * Wenn `ifexists` angegeben, wird das Verhalten des Befehls geändert, um das Umbenennen von Teilen nicht vorhandener Tabellen zu ignorieren.

**Anmerkungen**

Dieser Befehl wird nur für Tabellen der Datenbank im Gültigkeitsbereich bedient.
Tabellennamen können nicht mit Cluster- oder Datenbanknamen qualifiziert werden.

Dieser Befehl erstellt keine neuen Tabellen und entfernt auch keine vorhandenen Tabellen.
Die durch den Befehl beschriebene Transformation muss so sein, dass sich die Anzahl der Tabellen in der Datenbank nicht ändert.

Der **Befehl** unterstützt das Austauschen von Tabellennamen oder komplexere Permutationen, sofern sie die oben genannten Regeln einhalten. Erfassen Sie beispielsweise Daten in mehrere Stagingtabellen, und tauschen Sie sie dann in einer einzigen Transaktion in vorhandene Tabellen aus.

**Beispiele**

Stellen Sie sich eine `A`Datenbank `B` `C`mit `A_TEMP`den folgenden Tabellen vor: , , und .
Der folgende Befehl `A` `A_TEMP` wird ausgetauscht `A_TEMP` und (damit `A`die Tabelle nun aufgerufen wird, `NEWB`und `C` umgekehrt), in umbenennen `B` und beibehalten. 

```
.rename tables A=A_TEMP, NEWB=B, A_TEMP=A
``` 

Die folgende Befehlsfolge:
1. Erstellt eine neue temporäre Tabelle
1. Ersetzt eine vorhandene oder nicht vorhandene Tabelle durch die neue Tabelle

```
// Drop the temporary table if it exists
.drop table TempTable ifexists

// Create a new table
.set TempTable <| ...

// Swap the two tables
.rename tables TempTable=Table ifexists, Table=TempTable

// Drop the temporary table (which used to be Table) if it exists
.drop table TempTable ifexists
```