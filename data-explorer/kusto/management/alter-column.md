---
title: . Alter Column-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die Spalte "Alter" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: ecf0fa09438f8df5792d8826150d58f06360cace
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617866"
---
# <a name="alter-column"></a>.alter column

Ändert den Datentyp einer vorhandenen Tabellenspalte.

> [!WARNING]
> Beim Ändern des Datentyps einer Spalte werden alle bereits vorhandenen Daten in dieser Spalte, die nicht den neuen Datentyp aufweisen, in zukünftigen Abfragen ignoriert und durch einen [NULL-Wert](../query/scalar-data-types/null-values.md)ersetzt. Nach der `alter column`Verwendung von können diese Daten nicht wieder hergestellt werden, selbst wenn Sie einen anderen Befehl verwenden, um den Spaltentyp wieder auf einen vorherigen Wert zu ändern.
> Im [folgenden](#changing-column-type-without-data-loss) finden Sie die empfohlene Vorgehensweise zum Ändern des Typs einer Spalte ohne Datenverlust.

**Syntax** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *columnnewtype* `type` `=` *ColumnNewType*
 
**Beispiel** 

```kusto
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>Ändern des Spalten Typs ohne Datenverlust

Erstellen Sie eine neue, ordnungsgemäß typisierte Tabelle, um den Spaltentyp bei Beibehaltung der Verlaufs Daten zu ändern.

Führen Sie für `T1` jede Tabelle, in der Sie einen Spaltentyp ändern möchten, die folgenden Schritte aus:

1. Erstellen Sie eine `T1_prime` Tabelle mit dem richtigen Schema (den richtigen Spaltentypen).
1. Tauschen Sie die Tabellen mit dem Befehl [. rename Tables](rename-table-command.md) aus, der das Austauschen von Tabellennamen ermöglicht.

```kusto
.rename tables T_prime=T1, T1=T_prime
```

Wenn der-Befehl abgeschlossen ist, werden die neuen `T1` Datenflüsse an in den Datenfluss mit ordnungsgemäß eingegeben, `T1_prime`und die Verlaufs Daten sind in verfügbar.

Bis `T1_prime` die Daten aus dem Beibehaltungs Zeitfenster gehen, `T1` müssen Abfragen, die Sie berühren, geändert `T1_prime`werden, um Union mit auszuführen.