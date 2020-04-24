---
title: external_table() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird external_table() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 9fd03fb3c8452702c3db27a5e0466e8608c04eb9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515452"
---
# <a name="external_table"></a>external_table()

Verweist auf eine externe Tabelle nach Namen.

```kusto
external_table('StormEvent')
```

**Syntax**

`external_table``(` *TableName* `,` [ *MappingName* ]`)`

**Argumente**

* *TableName*: Der Name der externen Tabelle, die abgefragt wird.
  Es muss sich um ein Zeichenfolgenliteral `blob` `adl`handeln, das auf eine externe Tabelle der Art oder verweist. <!-- TODO: Document data formats supported -->

* *MappingName*: Ein optionaler Name des Zuordnungsobjekts, das die Felder in den tatsächlichen (externen) Daten-Shards den Spalten zuordnet, die von dieser Funktion ausgegeben werden.

**Hinweise**

Weitere Informationen zu externen Tabellen finden Sie in [externen Tabellen.](schema-entities/externaltables.md)

Siehe auch [Befehle zum Verwalten externer Tabellen](../management/externaltables.md).

Die `external_table` Funktion hat ähnliche Einschränkungen wie die [Tabellenfunktion.](tablefunction.md)