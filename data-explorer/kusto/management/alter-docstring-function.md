---
title: .alter-Funktion docstring - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt .alter function docstring in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 25f6dac67f65add545f7215b44daafb0257a8375
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522575"
---
# <a name="alter-function-docstring"></a>.alter-Funktion docstring

Ändert den DocString-Wert einer vorhandenen Funktion.

`.alter``function` *FunctionName-Dokumentation* `docstring` *Documentation*

> [!NOTE]
> * Erfordert [Datenbankadministratorberechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer,](../management/access-control/role-based-authorization.md) der die Funktion ursprünglich erstellt hat, darf die Funktion ändern. 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Funktion finden Sie unter [.create-Funktion](create-function.md)

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die für die Funktion erforderlichen Parameter.
|Body  |String |(Null oder mehr) `let` gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von UI-Funktionen verwendet wird. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke.

**Beispiel** 

```
.alter function MyFunction1 docstring "Updated docstring"
```
    
|name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: lang)| "StormEvents&#124; myLimit einschränken"|Myfolder|Aktualisierte Docstring|