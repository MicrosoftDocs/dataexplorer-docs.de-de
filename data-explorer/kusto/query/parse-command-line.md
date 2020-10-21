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
ms.openlocfilehash: a13e3def33ea7098a48db7ffefa4406097863c2a
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342721"
---
# <a name="parse_command_line"></a>parse_command_line()

Analysiert eine Unicode-Befehlszeilen Zeichenfolge und gibt ein dynamisches Array mit den Befehlszeilen Argumenten zurück.

## <a name="syntax"></a>Syntax

`parse_command_line(`*command_line**parser_type*`)`

## <a name="arguments"></a>Argumente

* *command_line*: die Befehlszeile, die analysiert werden soll.
* *parser_type*: der einzige derzeit unterstützte Wert ist `"Windows"` , der die Befehlszeile auf die gleiche Weise wie [commandlineumargvw](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)analysiert.

## <a name="returns"></a>Rückgabe

Ein dynamisches Array der Befehlszeilenargumente.

## <a name="example"></a>Beispiel

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|Ergebnis|
|---|
|["Echo", "Hello World!"]|