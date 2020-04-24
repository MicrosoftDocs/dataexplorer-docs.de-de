---
title: .alter-Funktionsordner - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt den .alter-Funktionsordner in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 140991c723ebdd12fa17000ea845adbbfdd27771
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522541"
---
# <a name="alter-function-folder"></a>.alter-Funktionsordner

Ändert den Ordnerwert einer vorhandenen Funktion.

`.alter``function` *FunctionName-Ordner* `folder` *Folder*

> [!NOTE]
> * Erfordert [Datenbankadministratorberechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer,](../management/access-control/role-based-authorization.md) der die Funktion ursprünglich erstellt hat, darf die Funktion ändern. 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Zum Erstellen einer neuen Funktion, [.create-Funktion](create-function.md)

|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Body  |String |(Null oder mehr) Lassen Sie Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von UI-Funktionen verwendet wird. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke.

**Beispiel** 

```
.alter function MyFunction1 folder "Updated Folder"
```
    
|name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: lang)| "StormEvents&#124; myLimit einschränken"|Aktualisierter Ordner|Einige DocString|

```
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: lang)| "StormEvents&#124; myLimit einschränken"|Erste Ebene, Zweite Ebene|Einige DocString|