---
title: .alter-Spalte - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die Spalte .alter in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d41b4f452125fbfebc319112db244deaca79f37a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522609"
---
# <a name="alter-column"></a>.alter-Spalte

Ändert den Datentyp einer vorhandenen Tabellenspalte.

> [!WARNING]
> Beim Ändern des Datentyps einer Spalte werden alle bereits vorhandenen Daten in dieser Spalte, die nicht vom neuen Datentyp sind, in zukünftigen Abfragen ignoriert und durch einen [NULL-Wert](../query/scalar-data-types/null-values.md)ersetzt. Nach `alter column`der Verwendung können diese Daten nicht wiederhergestellt werden, auch wenn Sie einen anderen Befehl verwenden, um den Spaltentyp wieder auf einen vorherigen Wert zu ändern.
> Siehe [unten](#changing-column-type-without-data-loss) für das empfohlene Verfahren zum Ändern des Typs einer Spalte, ohne Daten zu verlieren.

**Syntax** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *ColumnName* `type` `=` *ColumnNewType*
 
**Beispiel** 

```
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>Ändern des Spaltentyps ohne Datenverlust

Um den Spaltentyp zu ändern und gleichzeitig die Verlaufsdaten beizubehalten, erstellen Sie eine neue, ordnungsgemäß eingegebene Tabelle.

Führen Sie `T1` für jede Tabelle, in der Sie einen Spaltentyp ändern möchten, die folgenden Schritte aus:

1. Erstellen Sie `T1_prime` eine Tabelle mit dem richtigen Schema (den rechten Spaltentypen).
1. Tauschen Sie die Tabellen mit dem Befehl [.rename tables](rename-table-command.md) aus, der das Auswechseln von Tabellennamen ermöglicht.

```
.rename tables T_prime=T1, T1=T_prime
```

Wenn der Befehl abgeschlossen ist, `T1` werden die neuen Daten zu diesem korrekt `T1_prime`eingegeben, und die Verlaufsdaten sind in verfügbar.

Bis `T1_prime` Daten aus dem Aufbewahrungsfenster `T1` herausgehen, müssen Abfragen, die berühren, geändert werden, um eine Union mit `T1_prime`auszuführen.