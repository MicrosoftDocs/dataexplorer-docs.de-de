---
title: '. Alter-Funktion: Azure Daten-Explorer | Microsoft-Dokumentation'
description: In diesem Artikel wird die Funktion ". Alter" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d8248fdd9428df11a8e77316eec621102ddff9d6
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617798"
---
# <a name="alter-function"></a>.alter function

Ändert eine vorhandene Funktion und speichert Sie in den Daten Bank Metadaten.
Regeln für Parametertypen und CSL-Anweisungen sind identisch mit denen [ `let` ](../query/letstatement.md)für-Anweisungen.

**Syntax**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|Output-Parameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion.
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Body  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Methode, mit der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.

> [!NOTE]
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Funktion finden Sie unter [. Create-Funktion](create-function.md)
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Funktion ursprünglich erstellt hat, kann die Funktion ändern. 
> * Nicht alle Kusto-Typen werden in `let` -Anweisungen unterstützt. Folgende Typen werden unterstützt: String, Long, DateTime, TimeSpan und Double.
> * Verwenden `skipvalidation` Sie, um die semantische Validierung der Funktion zu überspringen. Dies ist nützlich, wenn Funktionen in falscher Reihenfolge erstellt werden und F1, das F2 verwendet, zuvor erstellt wurde.
 
**Beispiel** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|Name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|MyFolder|Demo Funktion mit Parameter|
