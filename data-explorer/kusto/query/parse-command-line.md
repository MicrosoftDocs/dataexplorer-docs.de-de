---
title: parse_command_line ()-Azure Daten-Explorer
description: In diesem Artikel wird parse_command_line () in Azure Daten-Explorer beschrieben.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: 0296b41dc10092f0b274491c3fab3355fc82a2d9
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2020
ms.locfileid: "85518135"
---
# <a name="parse_command_line"></a>parse_command_line ()

Analysiert eine Unicode-Befehlszeilen Zeichenfolge und gibt ein dynamisches Array mit den Befehlszeilen Argumenten zurück.

**Syntax**

`parse_command_line(`*command_line**parser_type*`)`

**Argumente**

* *command_line*: die Befehlszeile, die analysiert werden soll.
* *parser_type*: der einzige derzeit unterstützte Wert ist `"Windows"` , der die Befehlszeile auf die gleiche Weise wie [commandlineumargvw](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)analysiert.

**Rückgabe**

Ein dynamisches Array der Befehlszeilenargumente.

**Beispiel**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Ergebnis|
|---|
|["Echo", "Hello World!"]|
