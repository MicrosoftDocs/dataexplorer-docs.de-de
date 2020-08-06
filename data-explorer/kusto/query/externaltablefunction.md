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
ms.openlocfilehash: 13b244eb151d140e3626412188ac9bc9de242cc6
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802977"
---
# <a name="external_table"></a>external_table()

Verweist mit dem Namen auf eine externe Tabelle.

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * Die- `external_table` Funktion hat ähnliche Einschränkungen wie die [Table](tablefunction.md) -Funktion.
> * [Externe Tabellen](schema-entities/externaltables.md)
> * [Befehle zum Verwalten externer Tabellen](../management/externaltables.md)

## <a name="syntax"></a>Syntax

`external_table``(` *Tabellen* Name [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Argumente

* *TableName*: der Name der externen Tabelle, die abgefragt wird.
  Muss ein Zeichenfolgenliteral sein, das auf eine externe Tabelle der Art `blob` oder verweist `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: ein optionaler Name des Zuordnungs Objekts, das die Felder in den tatsächlichen (externen) datenshards den Spalten zuordnet, die von dieser Funktion ausgegeben werden.
