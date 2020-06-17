---
title: '. Anzeigen von Datenbanken: Azure Daten-Explorer'
description: In diesem Artikel wird beschrieben, wie Sie Datenbanken in Azure Daten-Explorer anzeigen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5ff64db05bb85d88ed3da3347ea95cf02b0234b
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818541"
---
# <a name="show-databases"></a>.show databases

Gibt eine Tabelle zurück, in der jeder Datensatz einer Datenbank im Cluster entspricht, auf die der Benutzer Zugriff hat.

Eine Tabelle, in der die Eigenschaften der Kontext Datenbank angezeigt werden, finden Sie unter [`.show database`](show-database.md) .

**Syntax**

`.show` `databases`

**Ausgabeschema**

|Spaltenname       |Spaltentyp|BESCHREIBUNG                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |Der Name der Datenbank, die an den Cluster angefügt ist.                    |
|Persistentstorage |`string`   |Der permanente Speicher "root" der Datenbank. Nur interne Verwendung          |
|Version           |`string`   |Die Version der Datenbank. Nur interne Verwendung                       |
|IsCurrent         |`bool`     |Gibt an, ob diese Datenbank der Daten Bank Kontext der Anforderung ist.                    |
|Databaseaccessmode|`string`   |Eines der-,-,- `ReadWrite` `ReadOnly` `ReadOnlyFollowing` oder-`ReadWriteEphemeral`    |
|Prettyname        |`string`   |Der Recht Name der Datenbank, falls vorhanden                        |
|ReservedSlot1     |`bool`     |Reserviert. Nur interne Verwendung              |
|Datenbank-ID (DatabaseId)        |`guid`     |Eine Globally Unique Identifier für die Datenbank. Nur interne Verwendung          |
