---
title: . Alter Function DocString-Azure Daten-Explorer | Microsoft-Dokumentation
description: In diesem Artikel wird die ". Alter Function DocString" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d590e93a6772483aba6b9580b26490eb2fe5ec08
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617849"
---
# <a name="alter-function-docstring"></a>. Alter Function DocString

Ändert den DocString-Wert einer vorhandenen Funktion.

`.alter``function` *FunctionName* `docstring` - *Dokumentation*

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Funktion ursprünglich erstellt hat, kann die Funktion ändern. 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Funktion finden Sie unter [. Create-Funktion](create-function.md)

|Output-Parameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Body  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Art und Weise, in der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.

**Beispiel** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|Name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|MyFolder|Aktualisierte DocString|