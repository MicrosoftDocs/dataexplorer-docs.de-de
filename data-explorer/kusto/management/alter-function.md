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
ms.openlocfilehash: fc7c29542f63ea9b659bd3318d1442d9bca4ae48
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321708"
---
# <a name="alter-function"></a>.alter function

Ändert eine vorhandene Funktion und speichert Sie in den Daten Bank Metadaten.
Regeln für Parametertypen und CSL-Anweisungen sind identisch mit denen für- [ `let` Anweisungen](../query/letstatement.md).

**Syntax**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|Ausgabeparameter |type |Beschreibung
|---|---|--- 
|Name  |String |Der Name der Funktion.
|Parameter  |String |Die Parameter, die für die Funktion erforderlich sind.
|Text  |String |(0 (null) oder mehr) `let` Anweisungen gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein für die Kategorisierung von Benutzeroberflächen Funktionen verwendeter Ordner. Dieser Parameter ändert nicht die Methode, mit der die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion zu UI-Zwecken.

> [!NOTE]
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Funktion finden Sie unter. [`.create function`](create-function.md)
> * Erfordert [Datenbankadministrator Berechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer](../management/access-control/role-based-authorization.md) , der die Funktion ursprünglich erstellt hat, kann die Funktion ändern. 
> * Nicht alle Kusto-Typen werden in-Anweisungen unterstützt `let` . Folgende Typen werden unterstützt: String, Long, DateTime, TimeSpan und Double.
> * Verwenden `skipvalidation` Sie, um die semantische Validierung der Funktion zu überspringen. Dies ist nützlich, wenn Funktionen in falscher Reihenfolge erstellt werden und F1, das F2 verwendet, zuvor erstellt wurde.
 
**Beispiel** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|Name |Parameter |Text|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(mylimit: Long)| {Stormevents &#124; Limit von mylimit}|MyFolder|Demo Funktion mit Parameter|
