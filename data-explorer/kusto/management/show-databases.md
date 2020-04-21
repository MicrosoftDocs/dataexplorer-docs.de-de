---
title: .anzeigen Datenbanken anzeigen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden .show-Datenbanken in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1827c3ea20d984c6846ef9feb603398020efe275
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610193"
---
# <a name="show-databases"></a>.Datenbanken anzeigen

Gibt eine Tabelle zurück, in der jeder Datensatz einer Datenbank im Cluster entspricht, auf die der Benutzer Zugriff hat.

Informationen zum Zurückgeben einer Tabelle mit den Eigenschaften der Kontextdatenbank finden Sie unter [.show database](show-database.md).

**Syntax**

`.show` `databases`

**Ausgabeschema**

|Spaltenname       |Spaltentyp|Beschreibung                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|DatabaseName      |`string`   |Der Name der Datenbank, die an den Cluster angefügt ist.                         |
|PersistentStorage |`string`   |Der persistente Speicher "Root" der Datenbank. (Nur für interne Verwendung.)      |
|Version           |`string`   |Die Version der Datenbank. (Nur für interne Verwendung.)                        |
|IsCurrent         |`bool`     |Gibt an, ob diese Datenbank der Datenbankkontext der Anforderung ist.                |
|DatabaseAccessMode|`string`   |Eine `ReadWrite`von `ReadOnly` `ReadOnlyFollowing`, `ReadWriteEphemeral`, , oder .|
|PrettyName        |`string`   |Der hübsche Name der Datenbank, falls vorhanden.                                    |
|ReservedSlot1     |`bool`     |Reserviert. (Nur für interne Verwendung.)                                           |
|Datenbank-ID (DatabaseId)        |`guid`     |Ein global eindeutiger Bezeichner für die Datenbank. (Nur für interne Verwendung.)      |
