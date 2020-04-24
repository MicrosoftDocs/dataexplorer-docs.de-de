---
title: .alter-Funktion - Azure Data Explorer | Microsoft Docs
description: Dieser Artikel beschreibt die .alter-Funktion in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 3fb7b3aab9d140d5f3660ef1384bf54efa9cd825
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522456"
---
# <a name="alter-function"></a>.alter-Funktion

Ändert eine vorhandene Funktion und speichert sie in den Datenbankmetadaten.
Die Regeln für Parametertypen und CSL-Anweisungen sind die gleichen wie für [ `let` Anweisungen](../query/letstatement.md).

**Syntax**

```
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|Ausgabeparameter |type |BESCHREIBUNG
|---|---|--- 
|Name  |String |Der Name der Funktion.
|Parameter  |String |Die für die Funktion erforderlichen Parameter.
|Body  |String |(Null oder mehr) `let` gefolgt von einem gültigen CSL-Ausdruck, der beim Funktionsaufruf ausgewertet wird.
|Ordner|String|Ein Ordner, der für die Kategorisierung von UI-Funktionen verwendet wird. Dieser Parameter ändert nicht die Art und Weise, wie die Funktion aufgerufen wird.
|DocString|String|Eine Beschreibung der Funktion für UI-Zwecke.

> [!NOTE]
> * Wenn die Funktion nicht vorhanden ist, wird ein Fehler zurückgegeben. Informationen zum Erstellen einer neuen Funktion finden Sie unter [.create-Funktion](create-function.md)
> * Erfordert [Datenbankadministratorberechtigung](../management/access-control/role-based-authorization.md)
> * Der [Datenbankbenutzer,](../management/access-control/role-based-authorization.md) der die Funktion ursprünglich erstellt hat, darf die Funktion ändern. 
> * Nicht alle Kusto-Typen `let` werden in Anweisungen unterstützt. Unterstützte Typen sind: String, Long, Datetime, Timespan und Double.
> * Verwenden `skipvalidation` Sie diese Option, um die semantische Validierung der Funktion zu überspringen. Dies ist nützlich, wenn Funktionen in einer falschen Reihenfolge und F1, das F2 verwendet, früher erstellt werden.
 
**Beispiel** 

```
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|name |Parameter |Body|Ordner|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: lang)| "StormEvents&#124; myLimit einschränken"|Myfolder|Demofunktion mit Parameter|