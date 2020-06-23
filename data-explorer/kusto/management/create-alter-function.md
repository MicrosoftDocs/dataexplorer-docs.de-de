---
title: . Create-or-Alter Function-Azure Daten-Explorer
description: In diesem Artikel wird die Funktion ". Create-or-Alter" in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f19ca38f344f10b9dd8e4491b467eaad5ca022bc
ms.sourcegitcommit: a034b6a795ed5e62865fcf9340906f91945b3971
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85197241"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

Erstellt eine gespeicherte Funktion oder ändert eine vorhandene Funktion und speichert Sie in den Daten Bank Metadaten.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

Wenn die Funktion mit dem angegebenen *FunctionName* nicht in den Daten Bank Metadaten vorhanden ist, erstellt der Befehl eine neue Funktion. Andernfalls wird diese Funktion geändert.

**Beispiel**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|Name|Parameter|Text|Ordner|DocString|
|---|---|---|---|---|
|TestFunction|(mylimit: int)|{Stormevents &#124; nehmen Sie mylimit}|MyFolder|Demo Funktion mit Parameter|
