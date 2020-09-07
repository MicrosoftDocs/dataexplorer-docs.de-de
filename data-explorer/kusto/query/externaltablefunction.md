---
title: external_table ()-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird external_table () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 30de02ba0ae18fbfd7944a97ad95d78dbe51066b
ms.sourcegitcommit: 08c54dabc1efe3d4e2d2581c4b668a6b73daf855
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2020
ms.locfileid: "89510677"
---
# <a name="external_table"></a>external_table()

Verweist mit dem Namen auf eine [externe Tabelle](schema-entities/externaltables.md) .

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * Die- `external_table` Funktion hat ähnliche Einschränkungen wie die [Table](tablefunction.md) -Funktion.
> * Standard mäßige [Abfrage Limits](../concepts/querylimits.md) gelten auch für externe Tabellen Abfragen.

## <a name="syntax"></a>Syntax

`external_table``(` *Tabellen* Name [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Argumente

* *TableName*: der Name der externen Tabelle, die abgefragt wird.
  Muss ein Zeichenfolgenliteral sein, das auf eine externe Tabelle der Art verweist `blob` , `adl` oder `sql` .

* *MappingName*: ein optionaler Name des Zuordnungs Objekts, das die Felder in den tatsächlichen (externen) datenshards den Spalten zuordnet, die von dieser Funktion ausgegeben werden.

## <a name="next-steps"></a>Nächste Schritte

* [Externe Tabelle (allgemeine Steuerungsbefehle)](../management/externaltables.md)
* [Erstellen und Ändern externer Tabellen in Azure Storage oder Azure Data Lake](../management/external-tables-azurestorage-azuredatalake.md)
* [Erstellen und Ändern externer SQL-Tabellen](../management/external-sql-tables.md)
