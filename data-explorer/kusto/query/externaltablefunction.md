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
ms.openlocfilehash: 4a3a1150996000742f5065df0eddc385074eaa48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348087"
---
# <a name="external_table"></a>external_table()

Verweist mit dem Namen auf eine externe Tabelle.

```kusto
external_table('StormEvent')
```

## <a name="syntax"></a>Syntax

`external_table``(` *Tabellen* Name [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Argumente

* *TableName*: der Name der externen Tabelle, die abgefragt wird.
  Muss ein Zeichenfolgenliteral sein, das auf eine externe Tabelle der Art `blob` oder verweist `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: ein optionaler Name des Zuordnungs Objekts, das die Felder in den tatsächlichen (externen) datenshards den Spalten zuordnet, die von dieser Funktion ausgegeben werden.

**Notizen**

Weitere Informationen zu externen Tabellen finden Sie unter [externe Tabellen](schema-entities/externaltables.md) .

Siehe auch [Befehle zum Verwalten externer Tabellen](../management/externaltables.md).

Die- `external_table` Funktion hat ähnliche Einschränkungen wie die [Table](tablefunction.md) -Funktion.