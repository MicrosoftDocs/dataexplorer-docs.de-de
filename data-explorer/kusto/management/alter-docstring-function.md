---
title: . Alter Function DocString-Azure Daten-Explorer
description: Dieser Artikel beschreibt `.alter function docstring` in Azure Daten-Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: cad374c767b126d60b7c701f596bddf3c20c4345
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763881"
---
# <a name="alter-function-docstring"></a>. Alter Function DocString

Ändert den `DocString` Wert einer vorhandenen Funktion.

`.alter``function` *FunctionName* - `docstring` *Dokumentation*

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Funktion ursprünglich erstellt hat, kann die Funktion ändern.
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Weitere Informationen zum Erstellen einer neuen Funktion finden Sie unter [. CREATE FUNCTION](create-function.md).

|Output-Parameter |Typ |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion.
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Text  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der ausgewertet wird, wenn die Funktion aufgerufen wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke

**Beispiel** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|name |Parameter |Text|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|MyFolder|Aktualisierte DocString|
