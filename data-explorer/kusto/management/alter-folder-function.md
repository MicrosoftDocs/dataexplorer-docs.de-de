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
ms.openlocfilehash: 37cedb7ba6e58f5b434101cd23b876cf18c2b78c
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321725"
---
# <a name="alter-function-folder"></a>.alter function folder

Ändert den Ordner Wert einer vorhandenen Funktion.

`.alter``function` *FunctionName* - `folder` *Ordner*

> [!NOTE]
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Funktion ursprünglich erstellt hat, kann die Funktion ändern. 
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Zum Erstellen einer neuen Funktion [`.create function`](create-function.md)

|Ausgabeparameter |type |Beschreibung
|---|---|--- 
|Name  |String |Der Name der Funktion. 
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Text  |String |(0 (null) oder mehr) Let-Anweisungen, gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von Benutzeroberflächen Funktionen verwendet wird. Dieser Parameter ändert nicht die Methode, mit der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.

**Beispiel** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|Name |Parameter |Text|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|Aktualisierter Ordner|Einige DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|Name |Parameter |Text|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|Erste level\zweite Ebene|Einige DocString|