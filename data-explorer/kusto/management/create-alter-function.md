---
title: .create-or-alter-Funktion - Azure Data Explorer | Microsoft Docs
description: In diesem Artikel wird die .create-or-alter-Funktion in Azure Data Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 9e9c24f7fda44d6c44b8f78d8622b525268a341a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744294"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

Erstellt eine gespeicherte Funktion oder ändert eine vorhandene Funktion und speichert sie in den Datenbankmetadaten.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

Wenn die Funktion mit dem bereitgestellten *FunctionName* in den Datenbankmetadaten nicht vorhanden ist, erstellt der Befehl eine neue Funktion. Wenn die Funktion bereits vorhanden ist, wird diese Funktion geändert.

**Beispiel**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|Name|Parameter|Body|Ordner|DocString|
|---|---|---|---|---|
|TestFunction|(myLimit:int)|• StormEvents &#124; myLimit nehmen|Myfolder|Demofunktion mit Parameter|
