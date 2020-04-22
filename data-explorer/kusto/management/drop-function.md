---
title: .drop-Funktion - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die .drop-Funktion in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8930f7333ff18fad0d5b3dbbebe9328f8bf7a0b9
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744791"
---
# <a name="drop-function"></a>.drop function

Löscht eine Funktion aus der Datenbank.
Informationen zum Löschen mehrerer Funktionen aus der Datenbank finden Sie unter [.drop-Funktionen](#drop-functions).

**Syntax**

`.drop``function` *Funktionsname* `ifexists`[ ]

* `ifexists`: Wenn angegeben, ändert sich das Verhalten des Befehls, um für eine nicht vorhandene Funktion nicht fehlschlagen zu können.

> [!NOTE]
> * Erfordert [Datenbankadministratorberechtigung](../management/access-control/role-based-authorization.md).
> * Der [Datenbankbenutzer,](../management/access-control/role-based-authorization.md) der die Funktion ursprünglich erstellt hat, darf die Funktion ändern.  
    
|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion, die entfernt wurde
 
**Beispiel** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>.drop-Funktionen

Löscht mehrere Funktionen aus der Datenbank.

**Syntax**

`.drop``functions` (*FunctionName1*, *FunctionName2*,..) [ifexists]

**Rückgabe**

Dieser Befehl gibt eine Liste der verbleibenden Funktionen in der Datenbank zurück.

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die für die Funktion erforderlichen Parameter.
|Body  |String |(Null oder mehr) `let` gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von UI-Funktionen verwendet wird. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke.

Erfordert [Funktionsadministratorberechtigung](../management/access-control/role-based-authorization.md).

**Beispiel** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
