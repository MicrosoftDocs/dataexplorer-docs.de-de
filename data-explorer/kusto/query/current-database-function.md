---
title: current_database() - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden current_database() in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c61717bbc8d202624b36088df5aed2ba3f3a8d2d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516744"
---
# <a name="current_database"></a>current_database()

Gibt den Namen der Datenbank im Bereich zurück (Datenbank, für die alle Abfrageentitäten aufgelöst werden, wenn keine andere Datenbank angegeben ist).

**Syntax**

`current_database()`

**Rückgabe**

Der Name der Datenbank im Bereich `string`als Wert des Typs .

**Beispiel**

```kusto
print strcat("Database in scope: ", current_database())
```