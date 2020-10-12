---
title: '. Drop-Funktion: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Drop-Funktion in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 279af228d15f511b35c26eebd0d21521450be786
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954738"
---
# <a name="drop-function"></a>.drop function

Löscht eine Funktion aus der Datenbank.
Informationen zum Löschen mehrerer Funktionen aus der Datenbank finden Sie unter [. Drop Functions](#drop-functions).

**Syntax**

`.drop``function` *FunctionName* [ `ifexists` ]

* `ifexists`: Wenn dies angegeben ist, wird das Verhalten des Befehls so geändert, dass es für eine nicht vorhandene Funktion nicht fehlschlägt.

> [!NOTE]
> * Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md)für die Funktion.
    
|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion, die entfernt wurde.
 
**Beispiel** 

```kusto
.drop function MyFunction1
```

## <a name="drop-functions"></a>. Drop-Funktionen

Löscht mehrere Funktionen aus der Datenbank.

**Syntax**

`.drop``functions`(*FunctionName1*, *FunctionName2*,..) [ifist vorhanden]

**Rückgabe**

Mit diesem Befehl wird eine Liste der verbleibenden Funktionen in der Datenbank zurückgegeben.

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Body  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Art und Weise, in der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.

Erfordert die [Administrator Berechtigung](../management/access-control/role-based-authorization.md)für die Funktion.

**Beispiel** 
 
```kusto
.drop functions (Function1, Function2, Function3) ifexists
```
