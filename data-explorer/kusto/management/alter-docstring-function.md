---
title: . Alter Function DocString-Azure Daten-Explorer
description: In diesem Artikel wird die ". Alter Function DocString" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: e364a3cc5607b1a4b629954c93bf90e9173c00f1
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321759"
---
# <a name="alter-function-docstring"></a>.alter function docstring

Ändert den `DocString` Wert einer vorhandenen Funktion.

`.alter``function` *FunctionName* - `docstring` *Dokumentation*

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Funktion ursprünglich erstellt hat, kann die Funktion ändern.
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Weitere Informationen zum Erstellen einer neuen Funktion finden Sie unter [`.create function`](create-function.md) .

|Ausgabeparameter |type |Beschreibung
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
    
|Name |Parameter |Text|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|MyFolder|Aktualisierte DocString|
