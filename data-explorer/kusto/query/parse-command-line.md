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
ms.openlocfilehash: f330c10e95cdc36eae497811ef895ef827918b43
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346489"
---
# <a name="parse_command_line"></a>parse_command_line()

Analysiert eine Unicode-Befehlszeilen Zeichenfolge und gibt ein dynamisches Array mit den Befehlszeilen Argumenten zurück.

## <a name="syntax"></a>Syntax

`parse_command_line(`*command_line**parser_type*`)`

## <a name="arguments"></a>Argumente

* *command_line*: die Befehlszeile, die analysiert werden soll.
* *parser_type*: der einzige derzeit unterstützte Wert ist `"Windows"` , der die Befehlszeile auf die gleiche Weise wie [commandlineumargvw](https://docs.microsoft.com/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)analysiert.

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
