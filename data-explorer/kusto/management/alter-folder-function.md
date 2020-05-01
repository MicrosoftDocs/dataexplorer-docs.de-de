---
title: '. Alter Function-Ordner: Azure Daten-Explorer | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt den Ordner "Alter Function" in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 6becb5e29fd5771e1027c824b5a3539ed3c33b88
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617832"
---
# <a name="alter-function-folder"></a>. Alter Function-Ordner

Ändert den Ordner Wert einer vorhandenen Funktion.

`.alter``function` *FunctionName* `folder` - *Ordner*

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Funktion ursprünglich erstellt hat, kann die Funktion ändern. 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Zum Erstellen einer neuen Funktion, [. Create-Funktion](create-function.md)

|Output-Parameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Body  |String |(0 (null) oder mehr) Let-Anweisungen, gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von Benutzeroberflächen Funktionen verwendet wird. Dieser Parameter ändert nicht die Methode, mit der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.

**Beispiel** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|Name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|Aktualisierter Ordner|Einige DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|Name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|Erste level\zweite Ebene|Einige DocString|