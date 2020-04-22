---
title: .create-Funktion - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die .create-Funktion in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc2caa5dcc886964b42bf1915bf3a003f2d32c7a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744460"
---
# <a name="create-function"></a>.create function

Erstellt eine gespeicherte Funktion, bei der es sich um eine wiederverwendbare [ `let` Anweisungsfunktion](../query/letstatement.md) mit dem angegebenen Namen handelt. Die Funktionsdefinition wird in den Datenbankmetadaten beibehalten.

Funktionen können andere Funktionen aufrufen (Rekursivität `let` wird nicht unterstützt), und Anweisungen sind als Teil des *Funktionstexts*zulässig. Siehe [ `let` Anweisungen](../query/letstatement.md).

Die Regeln für Parametertypen und CSL-Anweisungen sind die gleichen wie für [ `let` Anweisungen](../query/letstatement.md).
    
**Syntax**

`.create``function` [`ifnotexists`]`with` `(``docstring` `=` [ [`,` `folder` `=` *Dokumentation*]`,` `skipvalidation` `=` [ *FolderName*] `)`] ] [ 'true'] `)`] *Funktionsname* `(` *ParamName* `:` *ParamType* [`,` ...] `)` `{` *FunctionBody*`}`

**Ausgabe**    
    
|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die für die Funktion erforderlichen Parameter.
|Body  |String |(Null oder mehr) `let` gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von UI-Funktionen verwendet wird. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke.

> [!NOTE]
> * Wenn die Funktion bereits vorhanden ist:
>    * Wenn `ifnotexists` Flag angegeben ist, wird der Befehl ignoriert (keine Änderung angewendet).
>    * Wenn `ifnotexists` das Flag NICHT angegeben ist, wird ein Fehler zurückgegeben.
>    * Informationen zum Ändern einer vorhandenen Funktion finden Sie unter [.alter-Funktion](alter-function.md)
> * Erfordert [Datenbankbenutzerberechtigungen](../management/access-control/role-based-authorization.md).
> * Nicht alle Datentypen `let` werden in Anweisungen unterstützt. Unterstützte Typen sind: boolean, string, long, datetime, timespan, double und dynamic.
> * Verwenden Sie 'skipvalidation', um die semantische Validierung der Funktion zu überspringen. Dies ist nützlich, wenn Funktionen in einer falschen Reihenfolge und F1, das F2 verwendet, früher erstellt werden.

**Beispiele** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|Name|Parameter|Body|Ordner|DocString|
|---|---|---|---|---|
|MyFunction1|()|"StormEvents &#124; Limit 100"|Demo|Einfache Demo-Funktion|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|Name|Parameter|Body|Ordner|DocString|
|---|---|---|---|---|
|MyFunction2|(myLimit:lang)|"StormEvents&#124; myLimit einschränken"|Demo|Demofunktion mit Parameter|
