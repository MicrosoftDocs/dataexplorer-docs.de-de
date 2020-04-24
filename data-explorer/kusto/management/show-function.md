---
title: .show-Funktionen - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel werden die Funktionen der Anzeigen in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7acfdb96570b067b0461f5a788b55fc8274c03f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519838"
---
# <a name="show-functions"></a>.show Funktionen

Listet alle gespeicherten Funktionen in der aktuell ausgewählten Datenbank auf.
Um nur eine bestimmte Funktion zurückzugeben, finden Sie weitere Informationen unter [.show-Funktion](#show-function).

```
.show functions
```

Erfordert [Datenbankbenutzerberechtigungen](../management/access-control/role-based-authorization.md).
 
|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die für die Funktion erforderlichen Parameter.
|Body  |String |(Null oder mehr) `let` gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von UI-Funktionen verwendet wird. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke.
 
**Ausgabebeispiel** 

|name |Parameter|Body|Ordner|DocString
|---|---|---|---|---
|MyFunction1 |() | "StormEvents &#124; Limit 100"|Myfolder|Einfache Demo-Funktion|
|MyFunction2 |(myLimit: lang)| "StormEvents&#124; myLimit einschränken"|Myfolder|Demofunktion mit Parameter|
|MyFunction3 |() | • StormEvents(100)|Myfolder|Funktion, die eine andere Funktion aufruft||

## <a name="show-function"></a>.show-Funktion

```
.show function MyFunc1
```

Listet die Details einer bestimmten gespeicherten Funktion auf. Eine Liste **aller** Funktionen finden Sie unter [.show-Funktionen](#show-functions).

**Syntax**

`.show``function` *FunctionName*

**Ausgabe**

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die für die Funktion erforderlichen Parameter.
|Body  |String |(Null oder mehr) `let` gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von UI-Funktionen verwendet wird. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke.
 
> [!NOTE] 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben.
> * Erfordert [Datenbankbenutzerberechtigungen](../management/access-control/role-based-authorization.md).
 
**Beispiel** 

```
.show function MyFunction1 
```
    
|name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction1 |() | "StormEvents &#124; Limit 100"|Myfolder|Einfache Demo-Funktion